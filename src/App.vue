<template>
  <div class="common-layout">
    <el-container>
      <el-aside width="200px">导航栏</el-aside>
      <el-container>
        <el-header>爬虫任务表</el-header>
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

    const res = await axios.post("/tasks/list", query);

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
<style scoped></style>
