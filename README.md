# Streamlit Demo - Paralympics Analytics Dashboard

This demo demonstrates an architecture using **FastAPI** for the backend and **Streamlit** for the interactive frontend.


## How to Run

You will need to run the backend and frontend in separate terminal windows. 

### 0. Setup Environment
To set up the project environment, run the following command in your terminal to install the requirements :
```bash
pip install -r requirements.txt
```

### 1. Start the Backend Server
This starts the FastAPI server which serves the data.
```bash
uvicorn backend.main:app --reload
````

> *The API will be available at `http://127.0.0.1:8000`*

### 2\. Start the Frontend Application

In a new terminal window, launch the Streamlit dashboard.

```bash
streamlit run frontend/app.py
```

-----

## Implementation

### Backend

The backend a single API endpoint to get all data and used to read into a dataframe:

  * **Endpoint:** `/api/paralympics/all`
  * **Logic:** It reads the `paralympics.db` database, which is from `wk8examples`

### Frontend

The frontend is built using **Streamlit** to create an interactive and responsive analytics dashboard. 

**Features:**
* **Data Integration:** The app fetches the complete dataset from the FastAPI backend on startup. It utilizes Streamlit's `@st.cache_data` decorator to store the result, ensuring the backend is queried only once to optimize performance.
* **Layout:** The UI is organized using a Sidebar for global filtering (Event Type, Year Range) and Tabs to categorize insights into "Data Visualization", "Gender Analysis", and "Game Details".
* **Visualization:** The dashboard integrates **Altair** for interactive line charts and **Plotly** for geographic maps and bubble charts to provide different views of the data.

**Simulating API Logic Locally:**
To demonstrate how a frontend typically interacts with specific backend endpoints, the app includes helper functions that "mimic" standard backend REST API behavior:
* `local_get_all(df)`: Simulates fetching all records (`GET /games`).
* `local_get_by_id(df, game_id)`: Simulates fetching a single resource (`GET /games/{id}`).
* `local_filter_by_type_and_year(...)`: Simulates query parameters (`GET /games?type=summer&year_gt=1980`).
-----

## Streamlit Example

*Example: Creating the "Total Participants over Years" Line Chart*

This demonstrates the full pipeline from data fetching to visualization using Stramlit.

**Step 1: Load Data**

The app makes one call to the backend API to retrieve data.

```python
raw_df = load_data() # Calls requests.get(API_URL)
```

**Step 2: Create Interactive Sidebar (Optional)**
```python
st.sidebar.title("Filter Control")
```
```python
#Get Event Type
selected_type = st.sidebar.selectbox("Select Event Type", ["All", "Summer", "Winter"])

#Get Time Range
min_year = int(raw_df['year'].min())
max_year = int(raw_df['year'].max())
year_range = st.sidebar.slider("Select Year Range",
                                   min_year,
                                   max_year,
                                   (1960, max_year))
```

**Step 3: Filter Data (Optional)**

We use the helper function to filter data based on user sidebar inputs.

```python
 filtered_df = local_filter_by_type_and_year(raw_df, selected_type, year_range[0], year_range[1])
```

**Step 4: Generate Chart**

Using the `filtered_df`, we create the chart using Altair. 
Streamlit also offers a wide range of charting functions. For more details, please refer to the Streamlit Documentation https://docs.streamlit.io..

```python
# Creating the chart with the processed data
chart = alt.Chart(filtered_df).mark_line(point=True).encode(
    x=alt.X('year:O', title='Year'),
    y=alt.Y('participants_total:Q', title='Total Participants'),
    color='event_type:N'
)
st.altair_chart(chart, use_container_width=True)
```

-----

## Visualizations Examples

The dashboard have several interactive charts:

1.  **Total Participants over Years (Line Chart):** Visualizes the increase in total athletes participating in Summer vs. Winter games.
2.  **Sports vs. Countries (Bubble Chart):** Correlates the number of countries invloved and sports events, with bubble size representing participant count.
3.  **Global Host Locations (Map):** A geographic scatter plot showing where Paralympic games have been held around the world.
4.  **Gender Parity Analysis (Stacked Area Chart):** Compares the ratio of Male vs. Female athletes over historical timeline.
5.  **Game Details (Data View):** Able to select specific game and check their details.

<!-- end list -->
