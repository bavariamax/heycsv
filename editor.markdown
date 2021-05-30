---
title: CSV Editor
date: 2021-05-26 17:05:00 +02:00
position: 4
layout: editor
site-title: CSV Editor - HeyCSV
---

<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container-fluid">
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNavDropdown">
      <ul class="navbar-nav">
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" role="button" data-bs-toggle="dropdown" aria-expanded="false">
            Sample CSV files
          </a>
          <ul class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
            <li><a class="dropdown-item" href="#" onclick="onBtnDemo1()">Load 1000 Sample Companies</a></li>
            <li><a class="dropdown-item" href="#" onclick="onBtnDemo2()">Load 1000 Sample Products</a></li>
            <li><a class="dropdown-item" href="#" onclick="onBtnDemo3()">Load 1000 Sample Users</a></li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle bi-gear" href="#" id="navbarDropdownMenuLink" role="button" data-bs-toggle="dropdown" aria-expanded="false">
            Format Settings
          </a>
          <ul class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
            <li> <label>Delimiter:</label>
						<select name="delimiterInput" id="delimiterInput">
						  <option value=",">,</option>
						  <option value=";">;</option>
						  <option value="&#9;">TAB</option>
						  <option value="|">|</option>
						  <option value=" ">-space-</option>
						</select></li>
          	<li> <label>Quotes:</label>
						<select name="quotesInput" id="quotesInput">
						  <option value="&quot;">"</option>
						  <option value="">-none-</option>
						</select></li>
						<li><a class="dropdown-item disabled" id="reload" href="#" onclick="reloadData()">Reload Data</a></li>
          </ul>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle bi-download disabled" href="#" id="navbarExport" role="button" data-bs-toggle="dropdown" aria-expanded="false">
            Export
          </a>
          <ul class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
            <li><a class="dropdown-item" href="#" onclick="onBtnExportCsv()"> CSV Export</a></li>
            <li><a class="dropdown-item" href="#" onclick="onBtnExportExcel()"> Excel Export</a></li>
          </ul>
        </li>
        <li class="nav-item">
          <a class="nav-link bi-x-circle disabled" aria-current="page" id="navbarReset" href="#" onclick="onBtnReset()"> Reset</a>
        </li>
      </ul>
    </div>
  </div>
</nav>


<div class="wrapper" style="padding: 20px;">
<div id="inputArea" style="margin-bottom: 100px;">
	<div id="picker" style="height: 200px;"></div>
	<div style="padding: 30px;">Or paste text below:</div>
	<div><textarea id="pasteText" style="width:100%; height: 200px;"></textarea>
		<button onclick="inputGrid()">Load Text into Editor</button></div>
</div>


</div>

<div class="loadingDiv" style="display: none;">
  <div class="loadingSpinner">
    <div></div><div><div></div></div>
  </div>
</div>

<div id="gridWrapper" style="display: none;">
<div style="border-top: 1px solid #bdc3c7; background-color: rgb(245, 247, 247);">
	<div id="fileTitle" style="font-family: -apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Oxygen-Sans,Ubuntu,Cantarell,Helvetica Neue,sans-serif; font-size: 14px; padding-left: 40px; height: 32px; background-color: rgb(245, 247, 247); display: table-cell; vertical-align: middle;"></div>
</div>
<div style="border-top: 1px solid #bdc3c7;">
	<input type="text" oninput="onQuickFilterChanged()" id="quickFilter" placeholder="quick search..." style="font-family: -apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Oxygen-Sans,Ubuntu,Cantarell,Helvetica Neue,sans-serif; font-size: 12px; padding-left: 40px; line-height: normal; height: 32px; border: none; outline-width: 0; width: 100%; background-color: rgb(245, 247, 247);">
</div>
<div id="csvGrid" style="height: 1000px; width: 100%;" class="ag-theme-balham"></div>
</div>

<input type="hidden" value="" id="loadedUrl" />

<script src="//static.filestackapi.com/filestack-js/3.x.x/filestack.min.js"></script>
<script type="text/javascript" charset="utf-8">

//
// URL Parameter
// 
const queryString = window.location.search;
const urlParams = new URLSearchParams(queryString);

if(urlParams.has('s')){
	 const s = urlParams.get('s');
	 createGridUrl(s);
}

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
  document.querySelector(".wrapper").style.display = "block";
  document.querySelector("#fileTitle").innerHTML = "";
  document.querySelector("#loadedUrl").value = "";
  var element = document.getElementById("navbarExport");
  element.classList.add("disabled");
  element = document.getElementById("navbarReset");
  element.classList.add("disabled");
  element = document.getElementById("reload");
  element.classList.add("disabled");
  if(gridOptions.api) gridOptions.api.destroy();
  document.querySelector("#gridWrapper").style.display = "none";
}

function onBtnExportCsv() {
	var params = getExportParams("csv");
	gridOptions.api.exportDataAsCsv(params);
}

function onBtnExportExcel() {
	var params = getExportParams("xls");
	gridOptions.api.exportDataAsExcel(params);
}

function getExportParams(ending) {
  var delimiter = document.getElementById("delimiterInput").value;
  var quotes = document.getElementById("quotesInput").value;
  var supQuotes = false;
  if(quotes == "") supQuotes = true;
  return {
    fileName: formatDate() + "_export." + ending,
	  columnSeparator: delimiter,
	  suppressQuotes: supQuotes  
  };
}

function onBtnTest1() {
	console.log("Test 1");
	const columnDefs = gridOptions.api.getColumnDefs();
	const newColumnDefs = [];

	 for (var n = i = 0; n < columnDefs.length + 1; n++) {

		 	if(n == 1) {
					var newCol = { 
		              colId: "Col_" + n,
		              field: "new_" + n,
		              headerName: "New",
		              editable: true,
		              sortable: true,
				          filter: true,
				          resizable: true,
				          enableRowGroup: true,
				          enableValue: true,
				          enablePivot: true,
				          hide: false
		          	};
		      newColumnDefs.push(newCol);
			 } else {
			 	columnDefs[i].colId = "Col_" + n;
			  newColumnDefs.push(columnDefs[i]);
				++i;
			 }
		
	 }
	console.log(newColumnDefs);
	gridOptions.api.setColumnDefs(newColumnDefs);
}

function onBtnTest2() {
	console.log("Test 2");
	const columnDefs = gridOptions.api.getColumnDefs();
	console.log(columnDefs);
}

function setTitleRow(title, size) {
	var delimiter = document.getElementById("delimiterInput").value;
  var quotes = document.getElementById("quotesInput").value;
	document.querySelector("#fileTitle").innerHTML = title + " | size: " + size + " | data parsed with '" + delimiter + "' as column delimiter and '" + quotes + "' text quotes";
}

function reloadData() {
	if(gridOptions.api) gridOptions.api.destroy();
	// check if data got inputted in text area or via URL
	if(document.querySelector("#loadedUrl").value == "") {
		console.log("reload inputGrid");
		inputGrid();
	} else {
		console.log("reload: " + document.querySelector("#loadedUrl").value);
		createGridUrl(document.querySelector("#loadedUrl").value);
	}
}

// 
// Paste 
//

function inputGrid(){
	var textinput = document.getElementById("pasteText").value;
	setTitleRow("Text Input", humanFileSize(byteCount(textinput)));
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
            throw new Error('File too big, select something smaller than 100MB');
        }
    },
    fromSources: ["local_file_system", "url"],
    accept: ["text/*"],
    customText: {
    	'Select Files to Upload': 'Select CSV File to Upload',
    	'Drag and Drop, Copy and Paste Files': 'Drag and Drop CSV File to Upload or Click and Select'
    },
    onFileUploadFinished: file => {	
    	createGridUrl(file.url, file.originalFile.name);
    }
};

client.picker(options).open();


//
// Grid
// 
 	  // lookup the container we want the Grid to use
	  const eGridDiv = document.querySelector("#csvGrid");

 	  var gridOptions = {};

 	  function createGridUrl(url, file){
		  console.log("createGridUrl");

		  	//activate Spinner
		  	document.querySelector(".wrapper").style.display = "none";
		  	document.querySelector("#gridWrapper").style.display = "block";
		  	document.querySelector(".loadingDiv").style.display = "block";

	      const Http = new XMLHttpRequest();
	      Http.open("GET", url);
	      Http.send();

	      Http.onreadystatechange = function() {

	      	if(Http.readyState === XMLHttpRequest.DONE) {
		        var status = Http.status;
		        if (status === 0 || (status >= 200 && status < 400)) {
			        console.log("csv file load success");
							
							document.querySelector("#loadedUrl").value = url;

			        text = Http.responseText;
			        if(!file) file = url;
			        setTitleRow("<a href='" + url +"' target='_blank'>" + file + "</a>", humanFileSize(byteCount(text)));
			        createGrid(text);
			     } else {
			     		document.querySelector(".loadingDiv").style.display = "none";
      				console.log("Oh no! There has been an error with the request!"); 
    			}
	    	}
	      };
 	  }


      function createGrid(text){ 

      	var delimiter = document.getElementById("delimiterInput").value;
      	var quotes = document.getElementById("quotesInput").value;
      	var supQuotes = false;
      	if(quotes == "") supQuotes = true;


      	document.querySelector(".wrapper").style.display = "none";
      	document.querySelector(".loadingDiv").style.display = "none";
      	document.querySelector("#gridWrapper").style.display = "block";

				$('#csvGrid').height($(window).height() - 200);


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
	        defaultCsvExportParams: {
	        	fileName: formatDate() + "_export.csv",
	        	columnSeparator: delimiter,
	        	suppressQuotes: supQuotes  	
	        },
	        defaultExcelExportParams: {
	        	fileName: formatDate() + "_export.xls",
	        	author: "HeyCSV.com",
	        	sheetName: "HeyCSV.com",     	
	        },
	        defaultColDef: {
	          sortable: true,
	          filter: true,
	          resizable: true,
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
	        groupSelectsChildren: true,
	        undoRedoCellEditing: true,
    			undoRedoCellEditingLimit: 50,
	        animateRows: true,
	        suppressDragLeaveHidesColumns: true,
	        suppressMakeColumnVisibleAfterUnGroup: true,
	        suppressFieldDotNotation: true,
	        rowGroupPanelShow: "always",
	        sideBar: {
          		toolPanels: ["columns", "filters"]
        	},
        	sideBar: true,
        	onFirstDataRendered: onFirstDataRendered
	     };

	      // create the grid passing in the div to use together with the columns & data we want to use
	      new agGrid.Grid(eGridDiv, gridOptions);

	      // parse text to get csv rows
	      var csvRows = parseCSV(text, delimiter, quotes);
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

	    function onFirstDataRendered(params) {
  			
	    	// activate Nav Links
	    	var element = document.getElementById("navbarExport");
  			element.classList.remove("disabled");
  			element = document.getElementById("navbarReset");
  			element.classList.remove("disabled");
  			element = document.getElementById("reload");
  			element.classList.remove("disabled");

  			  var allColumnIds = [];
			  gridOptions.columnApi.getAllColumns().forEach(function (column) {
			    allColumnIds.push(column.colId);
			  });

  			gridOptions.columnApi.autoSizeColumns(allColumnIds, false);
		}

	 	function onQuickFilterChanged() {
  			gridOptions.api.setQuickFilter(document.getElementById('quickFilter').value);
	  	}	

      function createCols(headers) {
        console.log("createCols");

        var colCount = headers.length;
        var columns = [];

        for (var col = 0; col < colCount; col++) {
          if (col == 0) {
            var colDef = { 
              colId: "Col_" + col,
              field: headers[col], 
              hide: true,
              editable: false,
              headerName: headers[col], 
              rowDrag: true, 
              headerCheckboxSelection: true,
              checkboxSelection: true,
	          	checkboxSelection: function(params) {
                    // we put checkbox on the name if we are not doing grouping
                    return params.columnApi.getRowGroupColumns().length === 0;
                },
                headerCheckboxSelection: function(params) {
                    // we put checkbox on the name if we are not doing grouping
                    return params.columnApi.getRowGroupColumns().length === 0;
                },
                headerCheckboxSelectionFilteredOnly: true
            };

            var autoColDef = {
            	field: headers[col],  	
            	minWidth: 250,
		        	headerName: "Group",
		        	headerCheckboxSelection: true,
					    cellRendererParams: {
					        checkbox: true
					    }
            }

            gridOptions.api.setAutoGroupColumnDef(autoColDef);

          } else {
            var colDef = { 
              colId: "Col_" + col,
              field: headers[col],
              headerName: headers[col],
              editable: true
          	};
          }
          columns.push(colDef);
        }

        return columns;
      }


//
// CSV Parsing
//

	    function parseCSV(str, delimiter, quotes) {
	    	console.log("parse Start");
		    var arr = [];
		    var quote = false;
		    for (var row = col = c = 0; c < str.length; c++) {
		        var cc = str[c], nc = str[c+1];
		        arr[row] = arr[row] || [];
		        arr[row][col] = arr[row][col] || '';

		        // add additional incremental column to have # row 
		        if(row == 0 && col == 0) {
		        	arr[row][col] = "#Row";
		        	++col;
		        	--c;
		        	continue;
		        }
		        if(row != 0 && col == 0) {
		        	arr[row][col] = row;
		        	++col;
		        	--c;
		        	continue;
		        }

		        if (cc == quotes && quote && nc == quotes) { arr[row][col] += cc; ++c; continue; }  
		        if (cc == quotes) { quote = !quote; continue; }
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

//
// Helper
//

      function formatDate() {
		    var d = new Date(),
		        minute = '' + d.getMinutes(),
		        hour = '' + d.getHours(),
		        month = '' + (d.getMonth() + 1),
		        day = '' + d.getDate(),
		        year = d.getFullYear();

		    if (month.length < 2) 
		        month = '0' + month;
		    if (day.length < 2) 
		        day = '0' + day;

    		return year + month + day + hour + minute;
			}

			function byteCount(s) {
			    return encodeURI(s).split(/%..|./).length - 1;
			}

			function humanFileSize(size) {
    		var i = Math.floor( Math.log(size) / Math.log(1000) );
    		return ( size / Math.pow(1000, i) ).toFixed(2) * 1 + ' ' + ['B', 'kB', 'MB', 'GB', 'TB'][i];
			};
    </script>