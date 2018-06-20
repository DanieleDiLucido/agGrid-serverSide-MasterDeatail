<template>
  <div id="app">
    <div style="height: 500px; padding: 30px; box-sizing: border-box;">
      <ag-grid-vue style="width: 100%; height: 100%;" class="ag-theme-material"
        :gridOptions="gridOptions"
        :gridReady="onGridReady" 
        :columnDefs="columnDefs"
        :isFullWidthCell="isFullWidthCell"
        :getRowHeight="getRowHeight">
      </ag-grid-vue>
    </div>
</div>
</template>

<script>
import "ag-grid/dist/styles/ag-grid.css";
import "ag-grid/dist/styles/ag-theme-material.css";
import "ag-grid-enterprise";
import Vue from "vue";
import { AgGridVue } from "ag-grid-vue";


const DetailRowComponent = Vue.extend({
  render: function(createElement) {
    return createElement("h1", this.params.data.name);
  }
});

class BlotterDatasource {
  constructor(getRowsFunction) {
    this.getRowsFunction = getRowsFunction;
  }
  getRows(params) {
    this.getRowsFunction(params);
  }
}

export default {
  name: "app",
  components: {
    AgGridVue
  },
  data: function() {
    return {
      gridOptions: null,
      columnDefs: null,
      detailRowHeight: null,
      frameworkComponents: null
    };
  },
  mounted() {
    this.gridOptions.api.sizeColumnsToFit();
  },
  beforeMount() {
    this.gridOptions = {
      animateRows: true,
      rowModelType: "serverSide",
      fullWidthCellRendererFramework: DetailRowComponent,
      autoGroupColumnDef: {
        headerName: "",
        suppressMenu: true,
        width: 30
      },
      onGridSizeChanged: () => {
        this.gridOptions.api.sizeColumnsToFit();
      }
    };

    this.columnDefs = [
      {
        field: "id",
        rowGroup: true,
        hide: true
      },
      {
        field: "name",
      },
      { field: "account" },
      { field: "calls" },
      {
        field: "minutes",
        valueFormatter: "x.toLocaleString() + ' m'"
      }
    ];
  },
  methods: {
    isFullWidthCell(rowNode) {
      return rowNode.level === 1;
    },
    getRowHeight(params) {
      return params.node.level === 0 ? 50 : 150;
    },
    onGridReady(params) {
      fetch("https://raw.githubusercontent.com/ag-grid/ag-grid-docs/latest/src/javascript-grid-master-detail/custom-detail-with-grid/data/data.json")
        .then(response => response.json())
        .then(response => {
          const data = response.map(
            (el, index) => {
              el.id = index;
              return el;
            }
          );
          this.gridOptions.api.setServerSideDatasource({
            getRows: params => {
              const groupKeys = params.request.groupKeys;

              if (groupKeys.length === 0) {
                params.successCallback(data, -1);
              } else {
                //Call for detail row
                params.successCallback([params.parentNode.data], 1);
              }
            }
          });
        });
    }
  }
};
</script>
