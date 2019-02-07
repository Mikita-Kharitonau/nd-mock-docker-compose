# docker-compose for mock services
* [scheduler-mock-service](https://github.com/Mikita-Kharitonau/scheduler-mock-service)
* [results-mock-service](https://github.com/Mikita-Kharitonau/results-mock-service)
* [validation-service](https://github.com/Mikita-Kharitonau/validation-service)


## How to run:
##### Clone scheduler-mock-service, results-mock-service:
```
$ git clone https://github.com/Mikita-Kharitonau/scheduler-mock-service.git
$ git clone https://github.com/Mikita-Kharitonau/results-mock-service.git
$ git clone https://github.com/Mikita-Kharitonau/validation-service.git
```
##### Build local images:
```
$ cd scheduler-mock-service
$ docker build -t scheduler-mock-service .

$ cd results-mock-service
$ docker build -t results-mock-service .

$ cd validation-service
$ docker build -t validation-service .
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

## Scheduler mock service
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

## Results mock service
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

## Validation service
**POST** request to **http://127.0.0.1:8083/api/v1/report/lci/test** with body
```json
{
	"lciRequestBody": {
		"jobName": "lci_test",
		"campaignId": 1,
		"reportDate": "2019-01-09"
	},
	"shouldBeEqualTo": {
		"campaignId": 1,
		"reportDate": "2019-01-09",
		"numberOfVisitors": 2,
		"numberOfImpressions": 3
	}
}
```
will ask scheduler to run next report:
```json
{
		"jobName": "lci_test",
		"campaignId": 1,
		"reportDate": "2019-01-09"
}
```
and return such response immediately:
```json
{
    "status": "SCHEDULED",
    "tests": [],
    "message": "Can't run tests, report still running..., jobId 372b687fb78c46290fb2832ce08dd50151359f82"
}
```
If scheduler has such report finished, validation-service will recieve
report results from results-service and run tests:
```json
{
    "status": "FINISHED",
    "tests": [
        {
            "name": "results should be equal to given json",
            "status": "PASSED" // and "FAILED" in case of difference between 'shouldBeEqualTo' parameter and actual results.
        }
    ],
    "message": "Tests was successfully applied"
}
```

