<template>
  <div id="grid"></div>
</template>

<script>
import axios from "axios";
export default {
  setup() {
    const jsonData = ref(null);
    onMounted(async () => {
      try {
        const response = await axios.get("/calcData.json"); // JSON 데이터를 제공하는 API 엔드포인트 URL로 변경해야 합니다.
        jsonData.value = response.data;
      } catch (error) {
        console.error("Error fetching JSON data:", error);
      }
      let dsconfig = {
        data: jsonData.value,
        groups: [
          { field: "class", isOpen: true },
          { field: "math", isOpen: true },
        ],
      };

      let gridConfig = {
        dataSource: dsconfig,
        container: "#grid",
        width: "40%",
        height: "400px",

        columns: [
          { field: "class", width: 80 },
          { field: "name" },
          { field: "number" },
          {
            field: "korean",
            groups: [
              {
                group: "*",
                aggregates: [{ func: "total", payload: "name" }],
                header: { prefix: "합계: ", field: "total" },
              },
            ],
            //   width: 50,
          },
          { field: "english", width: 50 },
          {
            field: "math",
            groupTitle: {
              title: { prefix: "수학점수", suffix: " 점", field: "math" },
            },
            width: 50,
          },
        ],
        groupable: true,
        groupAutoOpen: false,
        navigatable: true,
      };
      const datagrid = SBGrid3.createGrid(gridConfig);
      datagrid.refresh();
    });
  },
};
</script>

<style></style>
