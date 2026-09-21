# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!


---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)


Kanika Goyal      CSE-C           202401100200162





Task 1:
AIOps Monitoring Scenario


This project shows the monitoring of a service. The service produces operational data, including metrics and logs.
The main problem is identifying unusual behavior early. When the service produces unexpected metric values or log information, the system should detect it and create an event that the operations team can investigate.
The purpose of AIOps in this assessment is to show how this process can be automated. The simulation takes operational data, detects anomalies, creates events, sends them from a producer to a topic, and then allows a consumer to process them and produce an AIOps result.
The workflow used in this project is:
Operational Data → Anomaly Detection → Event Generation → Producer → Topic → Consumer → AIOps Output


# Task 2:
## Analysis of the Operational Data
The data in 'data/service_data.json' belongs to the payment-service. It shows how the service behaved over a period of ten minutes.
The main metrics are 'response_time_ms', 'cpu_percent', and 'memory_percent'. These fields show how quickly the service responded and how much CPU and memory it was using.
The log information is contained in `log_level` and `message`. The `log_level` shows whether the record is informational or an error, while the message explains what happened.
Each record also has a 'timestamp'. The timestamps are one minute apart, starting at `10:00` and ending at `10:09` on `2026-09-20`. This makes it possible to follow the service behavior in the order it happened.
The records from '10:00' to '10:04' appear to show normal behavior. The response times are between 120 ms and 142 ms, CPU usage is between 42% and 48%, and memory usage is between 51% and 55%. The logs also show that payment requests were processed successfully.
The records from '10:07' to '10:09' also look normal because the metric values return to similar levels and the logs again show successful payment processing.


The unusual behavior occurs at '10:05' and '10:06'. At '10:05', the response time increases to 610 ms and the service reports a payment timeout. At '10:06', the response time is 640 ms, CPU usage reaches 94%, and memory usage reaches 91%. The log also reports a database connection timeout.
Based on the timestamps and values, the service experienced a short period of problems at '10:05' and '10:06', then returned to normal behavior at '10:07'.


Task 3:


I ran the provided pipeline using the operational data. It processed all 10 records and detected two anomalous observations.


The first anomaly occurred at 10:05. The response time was 610 ms, which was above the configured limit of 500 ms. The log also showed an error message: Payment service timeout.


The second anomaly occurred at `10:06`. The response time was 640 ms, CPU usage was 94%, and memory usage was 91%. These values were above the configured thresholds. The log message was Database connection timeout.
The normal records were not incorrectly flagged. The records before `10:05` and after `10:06` had normal metric values and successful `INFO` log messages.
But the detected events are not printed as topic of producer and consumer are different.
One expected issue was missed by the detector. The concerning records use the `ERROR` log level, but the detector only checks for `WARNING`. Because of this, the error logs were not included in the anomaly reasons.
The detected events were also not printed in the final output. The producer sends events to the `service-events` topic, while the consumer reads from the separate `anomaly-events` topic. Therefore, the consumer received zero events even though two anomalies were detected.
A possible improvement would be to make the detector recognise `ERROR` logs and ensure that the producer and consumer use the same topic.


## Task 4: Checking the Event Flow


I ran the pipeline to see whether an anomaly could move through the full event-processing workflow.


The detector found two anomalies in the data and created events for them. However, the consumer was listening to a different topic, 'anomaly-events'. Because the producer and consumer were using different topics, the consumer did not receive any events. The output showed that two anomalies were detected, but zero events were consumed.


This means the complete workflow did not work as expected. The anomaly detection and event creation parts worked, but the events did not reach the consumer or the final AIOps output.


The main components have these roles:
- Event: Stores information about the detected anomaly.
- Producer: Sends the event to a topic.
- Topic: Holds the event until it is consumed.
- Consumer: Reads and processes the event.
- AIOps output: Displays the events that were successfully processed.


The producer and consumer should use the same topic so that the detected events can reach the consumer and appear in the final output.


Task 5:
While testing the AIOps workflow, I found two problems.
The first problem was in the anomaly detector. It was checking for `WARNING` logs, but the service data uses `ERROR` for the records that contain problems. Because of this, the error messages were not being included in the anomaly reasons. I changed the detector to check for `ERROR` logs.


The second problem was in the connection between the producer and consumer. The producer was publishing events to `service-events`, while the consumer was listening to `anomaly-events`. These were separate topics, so the consumer did not receive the events. I corrected the pipeline so that both components use the same `EventTopic` object.


After making these changes, I ran the pipeline again. It processed all 10 records, detected 2 anomalies, and the consumer received both events. The events occurred at `10:05` and `10:06` and included the high metric values and error-log details.


This confirmed that the workflow now works from anomaly detection through to the final AIOps output. The existing architecture was kept in place, and only the incorrect log check and topic connection were corrected.

 Task 6: Running the Complete AIOps Pipeline


After fixing the issues found in the previous task, I ran the full AIOps pipeline again.


The pipeline processed all 10 operational data records and found two unusual observations at `10:05` and `10:06`. An event was created for each anomaly and sent to the producer.


The producer published both events to the shared `service-events` topic. The consumer received both events from that topic and processed them successfully.


The final output showed the details of the two problems in the payment service. These included the increased response times, high CPU and memory usage, and the error messages recorded at those times.


## Task 7: AIOps Assessment Summary


### AIOps Scenario
This project simulates monitoring a payment service. The service produces operational information in the form of metrics and logs. The aim is to detect unusual behaviour and turn it into an event that can be passed through an AIOps workflow.


The workflow used in this project is:
Operational Data → Anomaly Detection → Event → Producer → Topic → Consumer → AIOps Output


### Operational Data
The sample data is stored in `data/service_data.json`. It contains 10 records for the `payment-service`, with one record collected every minute between `10:00` and `10:09` on `2026-09-20`.


The metric fields are:- `response_time_ms`- `cpu_percent`- `memory_percent`
The log fields are:
- `log_level`
- `message`


The `timestamp` field shows when each observation was recorded and makes it possible to follow the service behaviour in time order.


### Observations from the Data
Most of the records show normal behaviour. From `10:00` to `10:04`, response times were between 120 ms and 142 ms, CPU usage was between 42% and 48%, and memory usage was between 51% and 55%. The logs showed that payment requests were processed successfully.
The records from `10:07` to `10:09` also returned to similar normal values.
The unusual behaviour occurred at `10:05` and `10:06`. At `10:05`, the response time increased to 610 ms and the log reported a payment service timeout. At `10:06`, the response time was 640 ms, CPU usage reached 94%, and memory usage reached 91%. The log at that time reported a database connection timeout.


### Anomaly Detection Results


The anomaly detector processed all 10 records and identified two anomalies.


The first anomaly was at `10:05`, because the response time was above the 500 ms threshold. The second anomaly was at `10:06`, because the response time, CPU usage, and memory usage were all above their configured thresholds.


The detector was originally checking for `WARNING` logs, but the data uses the `ERROR` level for the concerning records. I corrected this so that error logs are included in the anomaly reasons.


The normal records were not incorrectly flagged.


### Event-Processing Flow


When the detector finds an anomaly, it creates an event containing the timestamp, service name, event type, reasons, and original record.


The producer publishes this event to an in-memory topic. The consumer reads the event from the same topic and passes it to the final AIOps output.


The producer and consumer originally used separate topic objects. Although both topics had names, they each had their own message list, so the consumer received no events. I corrected the workflow so that both components use the same `EventTopic` object.


### Final Execution Result


After making the corrections, I ran the pipeline from the project root using:
  python aiops_pipeline.py


Task 8:
## Task 8: Validation
I reran the tests using the project root in the Python path:

```bash
PYTHONPATH=. python -m pytest

