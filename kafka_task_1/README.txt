# mannually install connector in power shell, sicne WSL(NOT WSL2) has bug on docker volume 
# for same reason docker-compose up in power shell, and sync update volume with windows path style in docker-compose.yml 

PS D:\b\hdp\examples\clickstream> docker run -v D:\b\HDP\examples\clickstream\confluent-hub-components:/share/confluent-hub-components confluentinc/ksqldb-server:0.8.0 confluent-hub install --no-prompt confluentinc/kafka-connect-datagen:0.3.2

PS D:\b\hdp\examples\clickstream> docker run -v D:\b\HDP\examples\clickstream\confluent-hub-components:/share/confluent-hub-components confluentinc/ksqldb-server:0.8.0 confluent-hub install --no-prompt confluentinc/kafka-connect-elasticsearch:5.5.0

PS D:\b\hdp\examples\clickstream> docker-compose down --volumes
PS D:\b\hdp\examples\clickstream> docker-compose up -d
PS D:\b\hdp\examples\clickstream> docker-compose ps

# make sure everything is there

PS D:\b\hdp\examples\clickstream> docker exec -it ksqldb-server ls -l /usr/share/confluent-hub-components/confluentinc-kafka-connect-datagen
PS D:\b\hdp\examples\clickstream> docker exec -it ksqldb-server ls -l /usr/share/confluent-hub-components/confluentinc-kafka-connect-elasticsearch
PS D:\b\hdp\examples\clickstream> docker exec -it ksqldb-server ls -l /scripts
 
# through ksqldb-cli to access connect ksqldb-server and run ddl (screen snap are ksqldb_001, ksqldb_002)
 
PS D:\b\hdp\examples\clickstream> docker-compose exec ksqldb-cli bash
root@6059dd8fd687:/# ksql http://ksqldb-server:8088
ksql> run script '/scripts/create-connectors.sql';
ksql> run script '/scripts/statements.sql';

# run scripts  (screen snap are script_001, script_002)
PS D:\b\hdp\examples\clickstream> docker-compose exec elasticsearch bash -c '/scripts/elastic-dynamic-template.sh'
PS D:\b\hdp\examples\clickstream> docker-compose exec ksqldb-server bash -c '/scripts/ksql-tables-to-grafana.sh'
PS D:\b\hdp\examples\clickstream> docker-compose exec grafana bash -c '/scripts/clickstream-analysis-dashboard.sh'



# munally start data generating 

PS D:\b\hdp\examples\clickstream> docker-compose exec ksqldb-server curl -X PUT localhost:8083/connectors/DATAGEN_CLICKSTREAM/pause
PS D:\b\hdp\examples\clickstream>  docker-compose exec ksqldb-server curl -X PUT localhost:8083/connectors/DATAGEN_CLICKSTREAM/resume
PS D:\b\hdp\examples\clickstream> docker-compose exec ksqldb-server curl -X PUT localhost:8083/connectors/DATAGEN_CLICKSTREAM/pause
PS D:\b\hdp\examples\clickstream>  docker-compose exec ksqldb-server curl -X PUT localhost:8083/connectors/DATAGEN_CLICKSTREAM/resume
PS D:\b\hdp\examples\clickstream> docker-compose exec ksqldb-server curl -X PUT localhost:8083/connectors/DATAGEN_CLICKSTREAM/pause