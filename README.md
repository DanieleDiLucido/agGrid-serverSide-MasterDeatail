# agGrid-serverSide-MasterDeatail-fix 
    
The goal of this repo is to present an easy and clean solution in order to solve the problem with the Master/Detail system that comeup using a **serverSide** rowModel.

> [from agGrid Documentation](https://www.ag-grid.com/javascript-grid-master-detail/#row-models) " The master grid (i.e. the top level grid) in Master / Detail can only be using the Client-side row model. It is not supported with Server-side, Viewport or Infinite row models. This is because all of these row models have their own unique way of loading data which would clash with the workings on master detail. The detail grid (i.e. the child grid) can use any of the row models. Thus as long as the master grid uses Client-side, then the detail grid can use any of the other row models. The reason for this is that the row expand and collapse is either not support (viewport and infinite row models) or has a different meaning (enterprise row model loads more rows when you expand). "
    
In this repo I solved this problem usig `Full Width Rows` and `Row Grouping` features.

### Enjoy 🖖
