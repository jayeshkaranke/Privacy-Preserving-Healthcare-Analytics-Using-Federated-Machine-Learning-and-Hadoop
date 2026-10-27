# Privacy-Preserving-Healthcare-Analytics-Using-Federated-Machine-Learning-and-Hadoop
 This project uses Machine Learning to estimate diabetes risk based on patient health data and provides health recommendations through a user-friendly dashboard. It uses Federated Learning to demonstrate privacy-preserving collaboration between hospitals and Hadoop for distributed healthcare data storage.
### Privacy-Preserving Healthcare Analytics Using Federated Machine Learning and Hadoop

This project presents a healthcare analytics web application that combines machine learning, federated learning, and distributed data storage concepts to support preliminary health risk assessment. The system provides an interactive dashboard, a risk analysis module, and a patient record management facility.

Users can enter basic personal information and health measurements, including age, glucose level, blood pressure, height, and weight. The application processes the information and uses a trained machine learning model to estimate diabetes risk. Based on the model's output, it displays a risk category and general health recommendations. Each completed assessment is automatically stored in an SQLite database for future reference.

A key aspect of the project is privacy-preserving collaborative learning. The system simulates three hospitals that train local machine learning models and share model parameters with a central coordinator. These parameters are aggregated to create a shared model, demonstrating how institutions can collaborate without directly exchanging raw patient records.

The project also incorporates Apache Hadoop concepts to demonstrate distributed storage and management of healthcare datasets. The overall system is developed using Python, Flask, Scikit-learn, SQLite, and web technologies.

The primary objective is to demonstrate how machine learning can support preliminary health risk screening while introducing privacy-conscious data collaboration and big data storage concepts. The application is an educational prototype and is not intended to replace professional medical diagnosis.
