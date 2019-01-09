# docker-compose for mock services
* [scheduler-mock-service](https://github.com/Mikita-Kharitonau/scheduler-mock-service)
* [results-mock-service](https://github.com/Mikita-Kharitonau/results-mock-service)


## How to run:
##### Clone scheduler-mock-service, results-mock-service:
```
$ git clone https://github.com/Mikita-Kharitonau/scheduler-mock-service.git
$ git clone https://github.com/Mikita-Kharitonau/results-mock-service.git
```
##### Build local images:
```
$ cd scheduler-mock-service
$ docker build -t scheduler-mock-service .

$ cd results-mock-service
$ docker build -t results-mock-service .
```
##### Clone this repo:
```
$ git clone https://github.com/Mikita-Kharitonau/nd-mock-docker-compose.git
```
##### Up services with mysql database:
```
$ cd nd-mock-docker-compose
$ docker-compose up
```

**POST** request to **http://127.0.0.1:8081/api/v1/report/lci** with body
```json
{
	"jobName": "test",
	"campaignId": 1,
	"reportDate": "2019-01-09"
}
```
will create a report row in lci_report table and return
```json
{
    "jobId": "d7f0161f03a5cd315f6ed775720781683041afa8",
    "status": "SCHEDULED"
}
``` 

It will updated after 30 sec simulating of report running.

**GET** request to **http://127.0.0.1:8081/api/v1/job/<job_id>** will return job status.
```json
{
    "jobId": "d7f0161f03a5cd315f6ed775720781683041afa8",
    "status": "SCHEDULED"
}
```

**GET** request to **http://127.0.0.1:8082/api/v1/job/<job_id>** will return full report results.

```json
{
    "jobName": "d7f0161f03a5cd315f6ed775720781683041afa8",
    "campaignId": 1,
    "reportDate": "2019-01-09",
    "numberOfVisitors": 2,
    "numberOfImpressions": 3,
    "status": "FINISHED"
}
```