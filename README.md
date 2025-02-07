# ETL Pipeline for Music Database

## **Purpose**
This project is an **end-to-end ETL pipeline** designed to extract, transform, and load music-related data from **Spotify's API** into a structured database. The primary goal is to apply data engineering principles in a real-world scenario, reinforcing my understanding of data processing, analytics, and storage. By leveraging **Microsoft Azure** services, this project also serves as a stepping stone toward achieving the **Azure Data Engineering Associate certification**.

---

## **Scope and Goals**
The project focuses on building a **music database** by:
1. Extracting artist, album, and track data from Spotify's API.
2. Transforming the data to ensure consistency and usability.
3. Loading the data into a structured database for analysis and visualization.
4. Simulating weekly streaming and download data to enrich the dataset.

The key objectives are:
- Gain hands-on experience with **Azure Data Factory**, **Databricks**, and **Azure SQL**.
- Develop a scalable and automated ETL pipeline.
- Create a foundation for future enhancements, such as user profiles and playlist generation.

---
### **Why Azure?**
The exclusive use of Azure services aligns with my goal of obtaining the **Azure Data Engineering Associate certification**. This project provides practical experience with Azure's ecosystem, including:
- **Azure Data Factory** for orchestration.
- **Azure Databricks** for scalable data processing.
- **Azure SQL** for relational database management.

---

## **Architecture**
### **High-Level Workflow**
1. **Data Extraction**:
   - Fetch artist, album, and track data from Spotify's API.
   - Simulate weekly streaming and download data using Python.
2. **Data Transformation**:
   - Clean, flatten, and standardize the data using Databricks.
   - Handle API limitations (e.g., rate limits) with retry mechanisms.
3. **Data Loading**:
   - Load transformed data into Azure SQL for analysis.
4. **Automation**:
   - Use Azure Data Factory triggers to automate pipeline execution.

### **Architecture Diagram**
![Architecture Diagram](path_to_architecture_diagram.png)

### **ER Diagram**
![ER Diagram](path_to_er_diagram.png)

---

## **Pipeline Implementation**
### **Data Extraction**
#### **Spotify API Integration**
- Authenticate using OAuth 2.0 and fetch data (artists, albums, tracks).
- Example: Fetching followed artists:
  ```python
  import spotipy
  from spotipy.oauth2 import SpotifyOAuth

  def fetch_followed_artists(client_id, client_secret, redirect_uri):
      sp = spotipy.Spotify(auth_manager=SpotifyOAuth(
          client_id=client_id,
          client_secret=client_secret,
          redirect_uri=redirect_uri,
          scope="user-follow-read"
      ))
      return sp.current_user_followed_artists(limit=20)
  ```

#### **Simulated Weekly Data**
- Generate fictional streaming and download data for tracks:
  ```python
  import numpy as np
  import json
  from datetime import datetime, timedelta

  def generate_weekly_data(song_list):
      weekly_data = {"songs": []}
      for song in song_list:
          downloads = int(np.random.normal(50000, 15000))
          streams = int(np.random.normal(600000, 200000))
          weekly_data["songs"].append({
              "song_id": song,
              "weekly_data": {
                  "total_weekly_downloads": max(downloads, 0),
                  "total_weekly_streams": max(streams, 0)
              }
          })
      return weekly_data
  ```

### **Data Transformation**
#### **Artist and Genre Data**
- Flatten nested JSON structures and extract relevant fields:
  ```python
  from pyspark.sql import Row

  def transform_artist_data(artist_json):
      artist_row = Row(
          ArtistID=artist_json['id'],
          Name=artist_json['name'],
          Followers=artist_json['followers']['total']
      )
      return artist_row
  ```

#### **Album and Track Data**
- Standardize release dates and convert track durations:
  ```python
  def transform_album_data(album_json):
      return Row(
          AlbumID=album_json['id'],
          AlbumName=album_json['name'],
          ReleaseDate=album_json['release_date']
      )
  ```

### **Data Loading**
- Load transformed data into Azure SQL:
  ```python
  df.write.format("jdbc") \
      .option("url", "jdbc:sqlserver://<your-database-url>") \
      .option("dbtable", "Albums") \
      .option("user", "<your-username>") \
      .option("password", "<your-password>") \
      .save()
  ```

---

## **Challenges and Solutions**
### **1. Spotify API Rate Limits**
- **Challenge**: Spotify's API imposes a limit of ~5000 requests per 30 seconds.
- **Solution**: Implemented a **retry mechanism** in Azure Data Factory to pause and resume extraction after rate limits are reached.

### **2. Lack of Bulk Endpoints**
- **Challenge**: Spotify does not provide bulk endpoints for tracks or albums.
- **Solution**: Used **Azure Databricks** to handle large-scale data processing and transformation.

### **3. Authentication with Spotify API**
- **Challenge**: Spotify's OAuth 2.0 flow requires user interaction.
- **Solution**: Used the **Spotipy** library to handle authentication locally and transfer data to Azure.

---
## **Tools and Technologies**
### **Primary Tools**
- **Azure Data Factory**: Orchestrating workflows and managing data pipelines.
- **Azure Databricks**: Performing advanced data transformations and processing.
- **Azure SQL**: Storing and querying structured data.
- **Spotify API**: Extracting music-related data (artists, albums, tracks).
- **Python**: Scripting for data extraction, transformation, and simulation.

## **Key Learnings**
- Gained hands-on experience with **Azure Data Factory** and **Databricks**.
- Reinforced **SQL skills** through database design and querying.
- Learned to handle **API rate limits** and **authentication** in real-world scenarios.
- Developed a deeper understanding of **ETL pipeline design** and **automation**.


## **Resources**
- [Spotify for Developers](https://developer.spotify.com/)
- [Azure Data Factory Documentation](https://learn.microsoft.com/en-us/azure/data-factory/)
- [Spotipy Python Library](https://spotipy.readthedocs.io/)
- [Azure Databricks Documentation](https://learn.microsoft.com/en-us/azure/databricks/)
- YouTube Tutorials: 1.Building an ETL Pipeline with Azure Data Factory(https://youtu.be/q_1IQF0-bsI)
                     2.Introduction to Azure Databricks for Data Engineering(https://youtu.be/QOLQHvgFY38)

---
