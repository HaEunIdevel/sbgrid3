<template>
  <div id="grid"></div>
</template>

<script>
import axios from "axios";
export default {
  setup() {
    const jsonData = ref(null);

    let dsconfig = {
      data: jsonData.value,
      groups: [{ field: "class", isOpen: true }],
    };

    let gridConfig = {
      dataSource: dsconfig,
      container: "#grid",
      width: "40%",
      height: "400px",

      columns: [
        { field: "class", width: 80, selectable: false }, // 해당 영역은 선택 지정되지 않음.
        { field: "name", selectable: false }, // 해당 영역은 선택 지정되지 않음.
        { field: "number", selectable: false }, // 해당 영역은 선택 지정되지 않음.
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
        { field: "math", width: 50 },
      ],
      groupable: true,
      groupAutoOpen: true,
      navigatable: true, // 키보드로 포커스 이동 가능하게 설정
      // 전체 selectable 설정
      selectable: {
        rowSelect: true,
        columnSelect: true,
        cellSelect: "cell",
        selectMode: "multi",
      },
      hover: "row", // 마우스 hover단위. cell, row단위로 설정 가능. default값 = cell
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
