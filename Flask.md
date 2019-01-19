# Flask 를 간단 하게 시작 하기  

## Flask 실행 하기  

- Sample Code-
```csharp
public override void WriteJson(JsonWriter writer, object value, JsonSerializer serializer)
            {
                JToken t = JToken.FromObject(value);

                if (t.Type != JTokenType.Object)
                {
                    t.WriteTo(writer);
                }
                else
                {
                    JObject o = (JObject)t;

                    var data = value as MyData;
                    if (data != null)
                    {
                        JObject obj = new JObject();

                        var columnNames = data.DataTable.Columns.Cast<DataColumn>()
                            .Select(x => x.ColumnName)
                            .ToList();

                        var rowItemJarray = new JArray();
                        foreach (DataRow dataTableRow in data.DataTable.Rows)
                        {
                            var oneRowItmes = new JArray(dataTableRow.ItemArray); 
                            rowItemJarray.Add(oneRowItmes);
                        }

                        var jarray = new JArray();
                        columnNames.ForEach(s => jarray.Add(s));



                        obj.Add(new JProperty("Cols", jarray));
                        obj.Add(new JProperty("rows", rowItemJarray));
                        obj.WriteTo(writer);
                    }
                    //IList<string> propertyNames = o.Properties().Select(p => p.Name).ToList();

                    //o.AddFirst(new JProperty("Keys", new JArray(propertyNames)));

                    //o.WriteTo(writer);
                }
            }
```
