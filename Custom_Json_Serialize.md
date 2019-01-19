# Json .net(Newtonsoft) 를 이용한 Custom Json Serialize 

## Serialize 하기 위한 사전지식

- **JToken**  
Json을 Serialize 할 때 여러 객체들이 있다 예를 들어 JObject, Jproperty, Jarray 등등 이런 클래스 들의 Base 클래스로 
생각 하면 좋을 듯 하다.

- **JObject**

Sample Code

```cs
       public class MyData
        {
            public DataTable DataTable { get; set; }
            public MyCore MyCore { get; set; }
        }

        public class MyCore
        {
            public List<double> Doubles { get; set; }
            public int Width { get; set; }
            public int Height { get; set; }

        }


        public class CustomSerialize : JsonConverter
        {
            private readonly Type[] _types;

            public CustomSerialize(params Type[] types)
            {
                _types = types;
            }

            public CustomSerialize()
            {

            }

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

            public override object ReadJson(JsonReader reader, 
                                            Type objectType, 
                                            object existingValue,
                                            JsonSerializer serializer)
            {
                throw new NotImplementedException("Unnecessary because CanRead is false. The type will skip the converter.");
            }

            public override bool CanRead
            {
                get { return false; }
            }

            public override bool CanConvert(Type objectType)
            {
                return _types.Any(t => t == objectType);
            }
        }

        static DataTable GetTable()
        {
            // Here we create a DataTable with four columns.
            DataTable table = new DataTable();
            table.Columns.Add("Dosage", typeof(int));
            table.Columns.Add("Drug", typeof(string));
            table.Columns.Add("Patient", typeof(string));
            table.Columns.Add("Date", typeof(DateTime));

            // Here we add five DataRows.
            table.Rows.Add(25, "Indocin", "David", DateTime.Now);
            table.Rows.Add(50, "Enebrel", "Sam", DateTime.Now);
            table.Rows.Add(10, "Hydralazine", "Christoff", DateTime.Now);
            table.Rows.Add(21, "Combivent", "Janet", DateTime.Now);
            table.Rows.Add(100, "Dilantin", "Melanie", DateTime.Now);
            return table;
        }
    }
```
Excute Code
``` cs
        private void ExcuteSerialize(object sender, EventArgs e)
        {

            var testData = new MyData();
            testData.DataTable = GetTable();
            testData.MyCore =new MyCore()
            {
                Doubles = new List<double>() { 1,2,3,4,5},
                Height = 5, 
                Width = 10,
            };
            
            var result =  JsonConvert.SerializeObject(testData, Formatting.Indented, new CustomSerialize(typeof(MyData)));
        }
```
