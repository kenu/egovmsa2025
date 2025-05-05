
| category | module                | dependency                  | check                                                                                |
| -------- | --------------------- | --------------------------- | ------------------------------------------------------------------------------------ |
| backend  | config                | rabbitmq                    | cloud:<br>  config:<br>    server:  <br>      native:  <br>        search-locations: |
|          | discovery             |                             |                                                                                      |
|          | apigateway            | config, discovery, rabbitmq |                                                                                      |
|          |                       |                             |                                                                                      |
|          | user-service          | mysql                       |                                                                                      |
|          | portal-service        | mysql                       |                                                                                      |
|          | board-service         | mysql                       |                                                                                      |
|          | reservation-*-service | mysql                       |                                                                                      |
|          |                       |                             |                                                                                      |
| frontend | admin                 | backend                     |                                                                                      |
|          | portal                | backend                     |                                                                                      |