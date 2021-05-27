---
title: CSV Editor
date: 2021-05-26 17:05:00 +02:00
position: 4
layout: editor
site-title: CSV Editor - HeyCSV
---

<div class="wrapper">
<div class="page-content">

<button onclick="onBtnDemo1()">Try Demo Companies</button>
<button onclick="onBtnDemo2()">Try Demo Products</button>
<button onclick="onBtnDemo3()">Try Demo Users</button>
<button onclick="onBtnReset()">Reset</button>
Delimiter: <input type="text" name="" id="delimiterInput" value="," style="width:30px; text-align: center;">

<div id="inputArea">
	<div id="picker" style="height: 200px;"></div>
	<div style="padding: 30px;">Or paste text below:</div>
	<div><textarea id="pasteText" style="width:100%; height: 200px;"></textarea>
		<button onclick="inputGrid()">Load Text into Editor</button></div>
</div>

</div>
</div>

<div id="csvGrid" style="height: 1000px; width: 100%; display: none;" class="ag-theme-balham"></div>

<script src="//static.filestackapi.com/filestack-js/3.x.x/filestack.min.js"></script>
<script type="text/javascript" charset="utf-8">


//
// Buttons
// 

function onBtnDemo1() {
  if(gridOptions.api) gridOptions.api.destroy();
  createGridUrl("https://www.heycsv.com/downloads/sample-csv/1k-sample-companies.csv");
}

function onBtnDemo2() {
  if(gridOptions.api) gridOptions.api.destroy();
  createGridUrl("https://www.heycsv.com/downloads/sample-csv/1k-sample-products.csv");
}

function onBtnDemo3() {
  if(gridOptions.api) gridOptions.api.destroy();
  createGridUrl("https://www.heycsv.com/downloads/sample-csv/1k-sample-users.csv");
}

function onBtnReset() {
  document.querySelector("#inputArea").style.display = "block";
  if(gridOptions.api) gridOptions.api.destroy();
  document.querySelector("#csvGrid").style.display = "none";
}

// 
// Paste 
//
function inputGrid(){
	var textinput = document.getElementById("pasteText").value;
	console.log(textinput);
	createGrid(textinput);
}


//
// Filestack
// 
const client = filestack.init("AaZv0uyTZmBD0H4g9mWbAz");

const options = {
	displayMode: "dropPane",
	container: "picker",
    onFileSelected: file => {
        // If you throw any error in this function it will reject the file selection.
        // The error message will be displayed to the user as an alert.
        if (file.size > 100000 * 1000) {
            throw new Error('File too big, select something smaller than 1MB');
        }
    },
    fromSources: ["local_file_system", "url"],
    accept: ["text/*"],
    customText: {
    	'Select Files to Upload': 'Select CSV File to Upload',
    	'Drag and Drop, Copy and Paste Files': 'Drag and Drop CSV File to Upload or Click and Select'
    },
    onFileUploadFinished: file => {	
    	createGridUrl(file.url);
    }
};

client.picker(options).open();



//
// Grid
// 
 	  // lookup the container we want the Grid to use
	  const eGridDiv = document.querySelector("#csvGrid");

 	  var gridOptions = {};

 	  function createGridUrl(url){
		  console.log("createGridUrl");

	      const Http = new XMLHttpRequest();
	      Http.open("GET", url);
	      Http.send();

	      Http.onreadystatechange = function() {

	      	if(Http.readyState === XMLHttpRequest.DONE) {
		        var status = Http.status;
		        if (status === 0 || (status >= 200 && status < 400)) {
			        console.log("csv file load success");
			        text = Http.responseText;
			        createGrid(text);
			     } else {
      				console.log("Oh no! There has been an error with the request!"); 
    			}
	    	}
	      };
 	  }


      function createGrid(text){ 

      	var delimiter = document.getElementById("delimiterInput").value;

      	document.querySelector("#inputArea").style.display = "none";
      	document.querySelector("#csvGrid").style.display = "block";

	      gridOptions = {
	        statusBar: {
	          statusPanels: [
	            {
	              statusPanel: "agTotalAndFilteredRowCountComponent",
	              key: "totalAndFilter",
	              align: "left"
	            },
	            { statusPanel: "agSelectedRowCountComponent", align: "left" },
	            { statusPanel: "agAggregationComponent", align: "right" }
	          ]
	        },
	        defaultColDef: {
	          minWidth: 50,
	          sortable: true,
	          filter: true,
	          resizable: true,
	          editable: true,
	          enableRowGroup: true,
	          enableValue: true,
	          enablePivot: true
	        },
	        rowSelection: "multiple",
	        singleClickEdit: false,
	        enableCellChangeFlash: true,
	        enableFillHandle: true,
	        debug: true,
	        enableRangeSelection: true,
	        rowDragManaged: true,
	        enableMultiRowDragging: true,
	        rowSelection: "multiple",
	        undoRedoCellEditing: true,
    		undoRedoCellEditingLimit: 50,
	        animateRows: true,
	        groupUseEntireRow: true,
	        suppressDragLeaveHidesColumns: true,
	        suppressMakeColumnVisibleAfterUnGroup: true,
	        rowGroupPanelShow: "always",
	        sideBar: {
          		toolPanels: ["columns", "filters"]
        	}
	     };

	      // create the grid passing in the div to use together with the columns & data we want to use
	      new agGrid.Grid(eGridDiv, gridOptions);

	      // parse text to get csv rows
	      var csvRows = parseCSV(text, delimiter);
	      console.log(csvRows[0].length + " Cols");
	      console.log(csvRows.length + " Rows");

	      // define header row and cols
	      var colDefs = createCols(csvRows[0]);
	      gridOptions.api.setColumnDefs(colDefs);
	      console.log(csvRows[0]);
	      

	      // create rows
	      var rowData = JSON.parse(parseCSVtoObjects(csvRows));
	      console.log(rowData);
	      gridOptions.api.setRowData(rowData);
	      console.log("rows created");

      }

      function createCols(headers) {
        console.log("createCols");

        var colCount = headers.length;
        var columns = [];

        for (var col = 0; col < colCount; col++) {
          if (col == 0) {
            var colDef = { 
              field: headers[col], 
              rowDrag: true, 
              headerCheckboxSelection: true,
              checkboxSelection: true };
          } else {
            var colDef = { field: headers[col] };
          }
          columns.push(colDef);
        }

        return columns;
      }

	    function parseCSV(str, delimiter) {
	    	console.log("parse Start");
		    var arr = [];
		    var quote = false;
		    for (var row = col = c = 0; c < str.length; c++) {
		        var cc = str[c], nc = str[c+1];
		        arr[row] = arr[row] || [];
		        arr[row][col] = arr[row][col] || '';

		        if (cc == '"' && quote && nc == '"') { arr[row][col] += cc; ++c; continue; }  
		        if (cc == '"') { quote = !quote; continue; }
		        if (cc == delimiter && !quote) { ++col; continue; }
		        if (cc == '\r' && nc == '\n' && !quote) { ++row; col = 0; ++c; continue; }
		        if (cc == '\n' && !quote) { ++row; col = 0; continue; }
		        if (cc == '\r' && !quote) { ++row; col = 0; continue; }

		        arr[row][col] += cc;
		    }
		    console.log("parse End");
		    return arr;
		}


      function parseCSVtoObjects(csvRows, /* optional */ columnNames) {
        console.log("CSVtoObject start");

        var firstDataRow = 0;
        if (!columnNames) {
          columnNames = csvRows[0];
          firstDataRow = 1;
        }

        var result = [];
        for (var i = firstDataRow, n = csvRows.length; i < n; i++) {
          var rowObject = {};
          var row = csvRows[i];
          for (
            var j = 0, m = Math.min(row.length, columnNames.length);
            j < m;
            j++
          ) {
            var columnName = columnNames[j];
            var columnValue = row[j];
            rowObject[columnName] = columnValue;
          }
          result.push(rowObject);
        }

        console.log("CSVtoObject end");
        return JSON.stringify(result); //JSON
      }


    </script>