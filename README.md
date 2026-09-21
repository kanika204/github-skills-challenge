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

