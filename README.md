# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!


---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)


# Task 1:
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


## Task 3: Anomaly Detection Results
I ran the provided pipeline using the operational data. It processed all 10 records and detected two anomalous observations.

The first anomaly occurred at 10:05. The response time was 610 ms, which was above the configured limit of 500 ms. The log also showed an error message: Payment service timeout.

The second anomaly occurred at `10:06`. The response time was 640 ms, CPU usage was 94%, and memory usage was 91%. These values were above the configured thresholds. The log message was Database connection timeout.
The normal records were not incorrectly flagged. The records before `10:05` and after `10:06` had normal metric values and successful `INFO` log messages.

But the detected events are not printed as topic of producer and consumer are different.
One expected issue was missed by the detector. The concerning records use the `ERROR` log level, but the detector only checks for `WARNING`. Because of this, the error logs were not included in the anomaly reasons.
The detected events were also not printed in the final output. The producer sends events to the `service-events` topic, while the consumer reads from the separate `anomaly-events` topic. Therefore, the consumer received zero events even though two anomalies were detected.
A possible improvement would be to make the detector recognise `ERROR` logs and ensure that the producer and consumer use the same topic.
