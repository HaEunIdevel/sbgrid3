<template>
  <div id="grid"></div>
</template>

<script>
import axios from "axios";
export default {
  setup() {
    const jsonData = ref(null);
    let gridConfig = {
      dataSource: jsonData.value,
      container: "#grid",
      width: "60%",
      height: "400px",

      columns: [
        { field: "class", width: 80 },
        { field: "name" },
        { field: "number" },
        { field: "korean", width: 50 },
        { field: "english", width: 50 },
        { field: "math", width: 50 },
      ],
    };
    // 데이터 변경을 감지하고 그에 따라 그리드를 생성
    watch(jsonData, () => {
      gridConfig.dataSource = jsonData.value;
      const datagrid = SBGrid3.createGrid(gridConfig);
      datagrid.refresh();
    });

    onMounted(async () => {
      try {
        const response = await axios.get("/calcData.json"); // JSON 데이터를 제공하는 API 엔드포인트 URL로 변경해야 합니다.
        jsonData.value = response.data;
      } catch (error) {
        console.error("Error fetching JSON data:", error);
      }
    });
  },
};
</script>

<style></style>
