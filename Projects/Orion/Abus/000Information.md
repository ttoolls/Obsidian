#### Prebuilt

export SYSROOTS=/opt/poky/active/sysroots/

From /build*: export CONFIG_PATH=$(find ../resources -name 'config.json')

####  Built
**imx8:**
cmake .. -DCMAKE_TOOLCHAIN_FILE=../cmake/poky-sdk-host86-64.cmake -DBUILD_WITH_TESTS=ON -DCMAKE_BUILD_TYPE=Debug -DENABLE_LOG_SENSITIVE=ON -DBUILD_WITH_CODECOVERAGE=ON

**x86**
cmake .. -DCMAKE_TOOLCHAIN_FILE=../cmake/poky-sdk-hostx86-64.cmake -DBUILD_WITH_TESTS=ON -DCMAKE_BUILD_TYPE=Debug -DENABLE_LOG_SENSITIVE=ON -DBUILD_WITH_CODECOVERAGE=ON

make -j6 aoc_debug_endpoints

#### QEMU

cd ~/Projects/Abus/aoc-device/tools/runqemu-wsl/
sudo ./runqemu.sh /mnt/c/Users/anseliva/Downloads/qemu_image/aoc-full-dev-qemuarm64-20231214064711.qemuboot.conf

ssh root@192.168.7.2

scp ./bin/aoc_debug_endpoints root@192.168.7.2:/opt/aoc/bin/

export CONFIG_PATH=/opt/aoc/data/config.json

export PATH=”/opt/aoc/bin:$(PATH)”

#### PyTest

Run for QEMU:
pytest --hostname="192.168.7.2" --port="8081" --login="Administrator" --password="Abus1924!" tests/systemtests/python_tests/tests/terminal/test_dev_base_sync_id_terminal.py

#### PostgreeSQL
service postgreesql start
**Connect**:
psql -U dbms_admin -d aoc_data

**Reset database**
sudo -u postgres -H sh -c ~/Projects/Abus/aoc-device/resources/database/experimental_hard_reset_db.sh

**SQL Queries**
SELECT * FROM control_data.t_auth_users

#### Postman

host_address 172.24.98.168
nabto_port 8081
login "Administrator"
pwd "Abus1924!"

Authorisation:
```
POST {{host_address}}:{{nabto_port}}/v1/session
Body:
{ "user_name": {{login}}, "password" : {{pwd}}}
Tests:
pm.test("authorization_test", function()
{
    pm.response.to.not.be.error;
    pm.response.to.have.status(200);
    pm.response.to.be.withBody;
    pm.response.to.be.json;
    var data = pm.response.json();
    pm.globals.set("jwt_value", data.access_token);
})
```

v1/debug_endpoints/sql_query body:
```
{
    "sql_queries": [ "SELECT COUNT(*) FROM control_data.t_auth_users", null, null ]
}
```

v1/debug_endpoints/mq_message body:
```
{
  "mqtt_messages": [
    {
        "topic" : "payload",
        "payload" : "payload"
    },
    {
        "topic": null,
        "payload": null
    },
    null,
    null
  ]
}
```



