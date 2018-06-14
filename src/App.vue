<template>
  <div id="app">
    <h5>FrontEndSide</h5>
    <div style="height: 50%; padding: 30px; box-sizing: border-box;">
      <ag-grid-vue style="width: 100%; height: 300px;" class="ag-theme-balham"
        :gridOptions="gridOptionsFE"
        :gridReady="onGridReady" 
        :columnDefs="columnDefs"
        :masterDetail="true"
        :detailRowHeight="detailRowHeight"
        :detailCellRenderer="detailCellRenderer"
        :frameworkComponents="frameworkComponents">
      </ag-grid-vue>
    </div>
    <h5>ServerSide</h5>
    <div style="height: 250px; padding: 30px; box-sizing: border-box;">
      <ag-grid-vue style="width: 100%; height: 100%;" class="ag-theme-balham"
        :gridOptions="gridOptionsBE"
        :gridReady="onGridReady" 
        :columnDefs="columnDefs"
        :masterDetail="true"
        :detailRowHeight="detailRowHeight"
        :detailCellRenderer="detailCellRenderer"
        :frameworkComponents="frameworkComponents">
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

// eslint-disable-next-line
const DetailCellRenderer = Vue.extend({
  render: function(createElement) {
    return createElement("h4", "Hi Guild");
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
      gridOptionsFE: null,
      gridOptionsBE: null,
      columnDefs: null,
      detailRowHeight: null,
      detailCellRenderer: null,
      frameworkComponents: null
    };
  },
  beforeMount() {
    this.gridOptionsFE = {};
    this.gridOptionsBE = {
      rowModelType: "serverSide",
      pagination: true,
      paginationPageSize:10,
    };
    this.columnDefs = [
      {
        field: "name",
        cellRenderer: "agGroupCellRenderer"
      },
      { field: "account" },
      { field: "calls" },
      {
        field: "minutes",
        valueFormatter: "x.toLocaleString() + ' m'"
      }
    ];
    this.detailRowHeight = 50;
    this.detailCellRenderer = "myDetailCellRenderer";
    this.frameworkComponents = { myDetailCellRenderer: DetailCellRenderer };

    this.datasource = new BlotterDatasource(enterpriseGetRowsParam =>
      this.populateTableFromServer(enterpriseGetRowsParam, this.moment)
    );
  },
  methods: {
    onGridReady(params) {
      const httpRequest = new XMLHttpRequest();
      httpRequest.open(
        "GET",
        "https://raw.githubusercontent.com/ag-grid/ag-grid-docs/latest/src/javascript-grid-master-detail/custom-detail-with-grid/data/data.json"
      );
      httpRequest.send();
      httpRequest.onreadystatechange = () => {
        if (httpRequest.readyState === 4 && httpRequest.status === 200) {
          if (!!params.api.serverSideRowModel) {
            // ServerSide
            const dataSource = {getRows: params => params.successCallback(JSON.parse(httpRequest.responseText),-1)};
            this.gridOptionsBE.api.setServerSideDatasource(dataSource);
          } else {
            // FE Side
            params.api.setRowData(JSON.parse(httpRequest.responseText));
          }
        }
      };
    }
  }
};
</script>

<style>

</style>
