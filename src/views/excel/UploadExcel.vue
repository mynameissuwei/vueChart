<template>
  <div class="app-container">
    <UploadExcelComponent
      :on-success="handleSuccess"
      :before-upload="beforeUpload"
    />
    <el-tabs v-model="activeName" style="margin-top: 15px" type="border-card">
      <el-tab-pane
        v-for="item in sheetName"
        :key="item"
        :label="item"
        :name="item"
      >
        <keep-alive>
          <el-table
            :data="tableData[activeName]"
            border
            highlight-current-row
            style="width: 100%;margin-top:20px;"
          >
            <!-- <el-table-column
              v-if="item === 'link'"
              v-for="item of tableHeader"
              :key="item"
              :prop="item"
              :label="item"
            /> -->
            <!-- <el-table-column prop="link" label="标签">
              <template slot-scope="scope">
                <a :href="scope.row.link"></a>
              </template>
            </el-table-column> -->
            <el-table-column
              v-for="item of tableHeader"
              :key="item"
              :label="item"
            >
              <template #default="scope">
                <a :href="scope.row[item]" v-if="item == 'link'" target="_blank"
                  >link</a
                >
                <span v-else> {{ scope.row[item] }} </span>
              </template>
            </el-table-column>
          </el-table>
        </keep-alive>
      </el-tab-pane>
    </el-tabs>
    <!-- <el-table
      :data="tableData[activeName]"
      border
      highlight-current-row
      style="width: 100%;margin-top:20px;"
    >
      <el-table-column
        v-for="item of tableHeader"
        :key="item"
        :prop="item"
        :label="item"
      />
    </el-table> -->
  </div>
</template>

<script lang="ts">
import { ElMessage } from "element-plus";
import { defineComponent, reactive, toRefs } from "vue";
import UploadExcelComponent from "@/components/up-excel/Index.vue";
export default defineComponent({
  components: {
    UploadExcelComponent
  },
  setup() {
    const dataMap = reactive({
      tableData: [],
      tableHeader: Array<String>(),
      sheetName: Array<String>(),
      activeName: "低溢价",
      beforeUpload: (file: File) => {
        const isLt1M = file.size / 1024 / 1024 < 1;
        if (isLt1M) {
          return true;
        }
        ElMessage.warning("Please do not upload files larger than 1m in size.");
        return false;
      },

      handleSuccess: ({
        results,
        header,
        sheetName
      }: {
        results: any;
        header: string[];
        sheetName: string[];
      }) => {
        console.log(header, "results");
        dataMap.tableData = results;
        dataMap.tableHeader = header;
        dataMap.sheetName = sheetName;
      }
    });
    console.log(dataMap.tableData, "sheetName");
    return { ...toRefs(dataMap) };
  }
});
</script>
