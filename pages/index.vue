<template>
  <div
    style="width: 400px; height: 500px; border: 1px solid black"
    id="box"
  ></div>
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
        // groups: [
        //   { field: "class", isOpen: true },
        //   { field: "math", isOpen: true },
        // ],
      };

      let gridConfig = {
        dataSource: dsconfig,
        container: "#grid",
        width: "40%",
        height: "400px",

        columns: [
          {
            field: "class",
            width: 80,
          },
          { field: "english", mergeColumn: true },
          {
            field: "math",
            // groupTitle: {
            //   title: { prefix: "수학점수", suffix: " 점", field: "math" },
            // },
            mergeColumn: true,
          },
          { field: "name" },
          { field: "number" },
          {
            field: "korean",
            sortable: false,
            // groups: [
            //   {
            //     group: "*",
            //     aggregates: [{ func: "total", payload: "name" }],
            //     header: { prefix: "합계: ", field: "total" },
            //   },
            // ],
            //   width: 50,
          },
        ],
        toolBar: ["add", "insert", "append", "delete"],
        groupable: true,
        groupAutoOpen: false,
        navigatable: true,
        editable: true, // edit 상태에서 하단으로 스크롤하시면 확인이 가능합니다.
        showSticky: true,
        stickyEdit: true,
        mergeRow: true,
        // mergeColumn: true,
        selectable: {
          rowSelect: true,
          columnSelect: true,
          cellSelect: "cell",
          selectMode: "multi",
        },
        copyable: true,
        copyInsert: true,
        sortable: "toggle",
        reorderable: { row: true, column: false },
      };
      const datagrid = SBGrid3.createGrid(gridConfig);

      datagrid.refresh();
      datagrid.bind("startdnd", "gridStartDnd");
      datagrid.bind("beforednd", "gridBeforeDnd");
      datagrid.bind("afterdnd", "gridAfterDnd");
    });

    function gridStartDnd() {
      document.getElementById("box").text("drag 일어날 때 이벤트 발생");
    }

    function gridBeforeDnd() {
      alert("drop 이전 이벤트 발생");
    }

    function gridAfterDnd() {
      alert("drop 이후 이벤트 발생");
      document.getElementById("box").text("");
    }
    return {
      gridStartDnd,
      gridBeforeDnd,
      gridAfterDnd,
    };
  },
};
</script>

<style></style>
