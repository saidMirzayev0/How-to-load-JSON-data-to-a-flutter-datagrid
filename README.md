# How to populate Flutter DataTable (SfDataGrid) with JSON API?


## Complate code
`Code Repository`: <https://github.com/saidMirzayev0/flutter-datagrid-JSON->

## STEP 1:
To fetch the JSON data from the web, add the following package in the dependencies of pubspec.yaml. 

<pre>
dependencies:
  http: ^0.12.0+4
</pre>

## STEP 2:
Import the following library in the flutter application.
 
 <pre>
import 'dart:convert';
import 'package:intl/intl.dart';
import 'package:http/http.dart' as http;
import 'package:flutter/material.dart';
import 'package:syncfusion_flutter_datagrid/datagrid.dart'; 
</pre>

## STEP 3:
To fetch the data from web API, create an async method. Get the JSON data using http.get method and decode that JSON data. And then, convert the JSON data to the list collection. Here a generateProductList() has been created for fetching JSON data.

<pre>
Future generateProductList() async {
    var response = await http.get(Uri.parse(
        'YOUR API URL'));
    var list = json.decode(response.body).cast<Map<String, dynamic>>();
    productlist =
        await list.map<_Product>((json) => _Product.fromJson(json)).toList();
    jsonDataGridSource = _JsonDataGridSource(productlist);
    return productlist;
  }
</pre>

## STEP 4:
Initialize the SfDataGrid with all the required properties. Here SfDataGrid is wrapped inside the FutureBuilder widget. Load the SfDataGrid  widget when the Snapshot.hasData contains data.

<pre>
@override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Flutter DataGrid Sample'),
      ),
      body: Container(
          child: FutureBuilder(
              future: generateProductList(),
              builder: (BuildContext context, AsyncSnapshot<dynamic> snapshot) {
                return snapshot.hasData
                    ? SfDataGrid(
                        source: jsonDataGridSource, columns: getColumns())
                    : Center(
                        child: CircularProgressIndicator(
                          strokeWidth: 3,
                        ),
                      );
              })),
    );
  }
</pre>

## STEP 5:
Create a data source class extends with DataGridSource for mapping data to the SfDataGrid.

<pre>
class _JsonDataGridSource extends DataGridSource {
  _JsonDataGridSource(this.productlist) {
    buildDataGridRow();
  }
 
  List<DataGridRow> dataGridRows = [];
  List<_Product> productlist = [];
 
 
  void buildDataGridRow() {
    dataGridRows = productlist.map<DataGridRow>((dataGridRow) {
      return DataGridRow(cells: [
        DataGridCell<int>(columnName: 'orderID', value: dataGridRow.orderID),
        DataGridCell<String>(
            columnName: 'customerID', value: dataGridRow.customerID),
        DataGridCell<int>(
            columnName: 'employeeID', value: dataGridRow.employeeID),
        DataGridCell<DateTime>(
            columnName: 'orderDate', value: dataGridRow.orderDate),
        DataGridCell<double>(columnName: 'freight', value: dataGridRow.freight),
      ]);
    }).toList(growable: false);
  }
 
  @override
  List<DataGridRow> get rows => dataGridRows;
 
  @override
  DataGridRowAdapter buildRow(DataGridRow row) {
    return DataGridRowAdapter(cells: [
      Container(
        alignment: Alignment.centerRight,
        padding: EdgeInsets.all(8.0),
        child: Text(
          row.getCells()[0].value.toString(),
          overflow: TextOverflow.ellipsis,
        ),
      ),
      Container(
        alignment: Alignment.centerLeft,
        padding: EdgeInsets.all(8.0),
        child: Text(
          row.getCells()[1].value,
          overflow: TextOverflow.ellipsis,
        ),
      ),
      Container(
        alignment: Alignment.centerRight,
        padding: EdgeInsets.all(8.0),
        child: Text(
          row.getCells()[2].value.toString(),
          overflow: TextOverflow.ellipsis,
        ),
      ),
      Container(
        alignment: Alignment.centerRight,
        padding: EdgeInsets.all(8.0),
        child: Text(
          DateFormat('MM/dd/yyyy').format(row.getCells()[3].value).toString(),
          overflow: TextOverflow.ellipsis,
        ),
      ),
      Container(
        alignment: Alignment.centerRight,
        padding: EdgeInsets.all(8.0),
        child: Text(
          row.getCells()[4].value.toString(),
          overflow: TextOverflow.ellipsis,
        ),
      ),
    ]);
  }
}
</pre>

