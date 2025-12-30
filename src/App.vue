<template>
  <div class="common-layout">
    <el-container>
      <el-aside width="200px">导航栏</el-aside>
      <el-container>
        <el-header>
          <el-input
            v-model="input1"
            style="max-width: 500px"
            placeholder="请输入"
            class="input-with-select"
            :disabled="select === 'false'"
          >
            <template #prepend>
              <el-select
                v-model="select"
                placeholder="请选择"
                style="width: 90px"
              >
                <el-option label="请选择" value="false">全部</el-option>
                <el-option label="source" value="source" />
                <el-option label="status" value="status" />
                <el-option label="type" value="type" />
              </el-select>
            </template>
            <template #append>
              <el-button :icon="Search" @click="loadTasks" />
            </template>
          </el-input>
          <el-button type="primary" @click="loadTasks">刷新</el-button>
        </el-header>
        <el-main>
          <div>
            <el-table :data="tasksData" style="width: 100%" height="450">
              <el-table-column fixed prop="id" label="任务ID" width="100" />
              <el-table-column prop="source" label="数据源" width="100" />
              <el-table-column prop="type" label="任务类型" width="100" />
              <el-table-column prop="status" label="任务状态" width="100" />
              <el-table-column prop="progress" label="任务进度" width="100" />
              <el-table-column prop="updated_at" label="更新时间" width="100" />
              <el-table-column label="操作" width="150">
                <template #default="scope">
                  <el-button
                    link
                    type="primary"
                    size="small"
                    v-if="scope.row.status === 'running'"
                    @click="restartTask(scope.row.id)"
                    >重启</el-button
                  >
                  <el-button
                    link
                    type="primary"
                    size="small"
                    v-if="
                      scope.row.status === 'stopped' ||
                      scope.row.status === 'finished'
                    "
                    @click="startTask(scope.row.id)"
                    >启动</el-button
                  >
                  <el-button
                    link
                    type="primary"
                    size="small"
                    v-if="scope.row.status === 'running'"
                    @click="stopTask(scope.row.id)"
                    >停止</el-button
                  >
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-main>
        <el-footer>
          <div>
            <el-pagination
              class="mt-4"
              sezer="small"
              background
              layout="prev, pager, next"
              :current-page="page"
              :page-size="limit"
              :total="total"
              @current-change="
                (p) => {
                  page = p;
                  loadTasks();
                }
              "
            />
          </div>
        </el-footer>
      </el-container>
    </el-container>
  </div>
</template>

<!-- js -->
<script setup>
import { ref, reactive, onMounted } from "vue";
import axios from "axios";

import { Search } from "@element-plus/icons-vue";

// 搜索框
const input1 = ref("");
const select = ref("false");

// 页面加载
onMounted(() => {
  loadTasks();
});

// 任务列表
const tasksData = ref([]);
const page = ref(1);
const limit = ref(10);
const total = ref(0);



// 获取任务列表
const loadTasks = async () => {
  try {
    let query = {
      page: page.value,
      limit: limit.value,
    };

    if (select.value === "source" && input1.value) {
      query.source = input1.value;
    }
    if (select.value === "status" && input1.value) {
      query.status = input1.value;
    }
    if (select.value === "type" && input1.value) {
      query.type = input1.value;
    }
    console.log(query);

    const res = await axios.post("/tasks/list", query);
    console.log(res);
    tasksData.value = res.data.data;
    total.value = res.data.total;
  } catch (err) {
    console.error("获取任务列表失败", err);
  }
};

// 重启任务
const restartTask = async (id) => {
  try {
    let query = {
      id: id,
    };
    const res = await axios.post("/tasks/restart", query);
    console.log(res);
    loadTasks();
  } catch (err) {
    console.error("重启任务失败", err);
  }
};

// 启动任务
const startTask = async (id) => {
  try {
    let query = {
      id: id,
    };
    const res = await axios.post("/tasks/start", query);
    console.log(res);
    loadTasks();
  } catch (err) {
    console.error("启动任务失败", err);
  }
};

// 停止任务
const stopTask = async (id) => {
  try {
    let query = {
      id: id,
    };
    const res = await axios.post("/tasks/stop", query);
    console.log(res);
    loadTasks();
  } catch (err) {
    console.error("停止任务失败", err);
  }
};
</script>

<!-- css -->
<style scoped>
/* 搜索框样式 */
.input-with-select .el-input-group__prepend {
  background-color: var(--el-fill-color-blank);
}
</style>
