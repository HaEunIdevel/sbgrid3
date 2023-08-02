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
        { field: "class", width: 80, selectable: false, mergeRow: true }, // 해당 영역은 선택 지정되지 않음.
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
        {
          field: "sum",
          format: "#,###",
          editable: false,
          calc: {
            require: ["korean", "english", "math"],
            eval: function (data) {
              return (
                parseFloat(data.korean) + // parseFloat 은 문자열을 전체를 실수(숫자)로 바꿈
                parseFloat(data.english) +
                parseFloat(data.math)
              );
            },
          },
        },
        {
          field: "avg",
          format: "#,##0.00", // #,### => 00,000  /// #,##0.00 => 00.00
          calc: {
            require: ["sum"],
            eval: data => data.sum / 3,
          },
        },
        {
          field: "grade",
          editable: false,
          calc: {
            require: ["avg"],
            eval: data => {
              if (data.avg > 90) return "A";
              if (data.avg > 80) return "B";
              if (data.avg > 70) return "C";
              if (data.avg > 60) return "D";
            },
          },
        },
        {
          field: "rank",
          editable: false,
          calc: {
            require: ["sum"],
            rows: rows => {
              rows.sort((a, b) => b.sum - a.sum);
              rows.forEach((row, index) => (row.rank = index + 1));
            },
          },
        },
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
      //   stickyRow: true,
      mergeRow: true, //행(가로) 단위 병합여부 true병합설정 // 여기에도 설정 추가해주어야하고, 원하는 해당 컬럼 부분에도 이 설정 추가해줘야 적용됨.
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
