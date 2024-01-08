test_debug_endpoints/py:
```

def test_WHEN_execute_sql_query_THEN_results_are_visible(self):

        insert_payload = 'INSERT INTO control_data.t_auth_users; VALUES (\'tectiq_administrator_1_local@abus-sc.com\', true, true, false, \'Anton\', \'Admin\');'

        sql_msg = json.dumps(insert_payload, separators=(',', ':'))

        self.debug_endpoints_client.post_sql_queries(sql_msg)

  

        select_payload = {}

        sql_msg = json.dumps(select_payload, separators=(',', ':'))

        self.debug_endpoints_client.post_sql_queries(sql_msg)

  

        delete_payload = {}

        sql_msg = json.dumps(delete_payload, separators=(',', ':'))

        self.debug_endpoints_client.post_sql_queries(sql_msg)
```


debug_endpoints_client.py:
```
 def post_sql_queries(self, sql_queries) -> Any:

        body = {}

        body['sql_queries'] = sql_queries

        sql_msg = json.dumps(body, separators=(',', ':'))

  

        response = self.__control_session.post('/v1/debug_endpoints/sql_query', sql_msg)

        response.raise_for_status()

        return response.json()
```