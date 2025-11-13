Forecasting Air Quality in Nakuru (An ARIMA Time Series Story)

Hello! Welcome to my project. This was a complete, top-to-bottom data science challenge where I rebuilt one of my favorite projects from my CV: forecasting air quality in Africa.
I started with a raw, messy .csv file and ended with a fully tuned, high-performance ARIMA model. This notebook is the story of that journey, showing how I battled messy data, found hidden patterns, and strategically built a model that works.

Data Source
The data for this project was downloaded from open.africa (the sensors.AFRICA archive), a fantastic open-source platform for air quality data.
Direct Link to Data: sensors.AFRICA Air Quality Archive - Nakuru (You can download it from Open Africa November 2025 Sensor Data Archive)
Raw File Used: Nakuru.csv (downloaded from the source above)

The Final Result 
I'm thrilled with how this turned out. After all the cleaning and tuning, I built a final model that improved predictive performance by 26.2% over a simple baseline.
Model
MAE (Test Set)
Improvement
Baseline (Lag=1)
10.74
-
Final Tuned ARIMA(24, 0, 2)
7.92
26.2%

This project shows my ability to take a real-world problem and see it through, from identifying key patterns (like the 24-hour cycle) to building a robust model that delivers real, measurable improvements.

 My Project Journey (The "How-To")

The complete, step-by-step analysis (with all my "Aha!" moments) is in the air-quality-analysis.ipynb notebook. Here’s the short story:
Battling the Data: The raw .csv file was a mess! The first challenge was just loading it. I had to battle ParserErrors by figuring out the file used semicolons (;) as separators and commas (,) for decimals. I also found corrupt text (like "17.?5") that I had to handle.
Preparing for Time Series: Once clean, I resampled the high-frequency data into stable 1-hour averages, converted the timezone to 'Africa/Nairobi', and made sure all my data types were correct.
The "A-ha!" Moment: The big breakthrough came from plotting the ACF/PACF charts. The PACF plot showed a massive spike at lag 24. This wasn't a guess; it was a data-driven clue that the 24-hour daily cycle was the most powerful predictor.
Strategic Tuning (Not Guessing!): Armed with that "lag 24" clue, I ran a targeted grid search to find the best model. The winner was ARIMA(24, 0, 2). This model is smart: it looks at the 24-hour cycle (p=24) and learns from its last two mistakes (q=2).
Final Validation: To prove the model worked, I ran a robust Walk-Forward Validation (WFV). This simulates a real-world scenario and confirmed the model's power, giving us our final 26.2% improvement on unseen test data.
️
Tech Stack (The Tools)
Python
pandas: For all data wrangling and cleaning.
statsmodels: For the ARIMA model and ACF/PACF statistical plots.
scikit-learn: For the LinearRegression baseline and mean_absolute_error.
matplotlib: For all visualizations.
Jupyter Notebook: For the analysis and final report.
tqdm: For those awesome progress bars!
pyarrow: For saving/loading the clean .feather file.

How to Run This Project
Clone this repository.
Go to the open.africa data source and download the .csv file. Place it in the project folder and name it Nakuru.csv.
Create and activate a virtual environment.
Install all the required libraries:
pip install -r requirements.txt


Start Jupyter Notebook:
jupyter notebook


Open the air-quality-analysis.ipynb file and run the cells!
