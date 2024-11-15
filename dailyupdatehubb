import requests
import streamlit as st
import datetime
from streamlit_autorefresh import st_autorefresh
from bs4 import BeautifulSoup
from textblob import TextBlob

# Header Image
st.image("https://i.imgur.com/p3y7PUO.jpeg")

# CSS for background and footer
st.markdown(
    """
    <style>
    .stApp {
        background-image: url("https://images.unsplash.com/photo-1582712286210-1fe282fe6cfc");
        background-size: cover;
        background-position: center;
    }

    .css-1n0htl8 { 
        background-color: rgba(255, 255, 255, 0.7); 
        padding: 20px;
        border-radius: 8px;
    }

    footer {
        visibility: hidden;
    }

    .footer {
        position: fixed;
        bottom: 0;
        left: 0;
        right: 0;
        background-color: rgba(0, 0, 0, 0.8);
        color: white;
        text-align: center;
        padding: 10px;
        font-size: 14px;
    }
    </style>
    """,
    unsafe_allow_html=True
)

# Auto-refresh setup for news updates
st_autorefresh(interval=300000, key="data_refresh")

# Title and Description
st.title("Welcome To Daily Update Hub")
st.write(
    "Here you will find all kinds of news like health updates, cooking updates, daily lifestyle updates, national and international news, and much more."
)

# Monetization: Placeholder for Ads
st.markdown(
    """
    <div style="border: 1px solid #ccc; padding: 10px; text-align: center;">
        <h4>Sponsored Advertisement</h4>
        <p>Your Ad Here</p>
    </div>
    """,
    unsafe_allow_html=True
)

# News API setup
API_KEY = "ae264a6d304344109cc583d9df65fc75"
BASE_URL = "https://newsapi.org/v2/top-headlines"

# Date Input for filtering
start_date = st.date_input("Start date", datetime.date(2024, 1, 1))
end_date = st.date_input("End date", datetime.date(2024, 12, 31))

# Category selection for news
categories = ["business", "entertainment", "health", "science", "sports", "technology"]
selected_categories = st.multiselect("Select categories", categories, default=categories)
categories_query = "&".join([f"category={cat}" for cat in selected_categories])

# Search query for news
query = st.text_input("Search for news")
if query:
    url = f"https://newsapi.org/v2/everything?q={query}&apiKey={API_KEY}"
else:
    url = f"{BASE_URL}?country=us&apiKey={API_KEY}&{categories_query}"

# Adding loading spinner while fetching data
with st.spinner('Fetching news articles...'):
    try:
        # Fetching news articles from the API
        response = requests.get(url)
        response.raise_for_status()  # Raise error for bad response (4xx, 5xx)
        articles = response.json().get("articles", [])
    except requests.exceptions.RequestException as e:
        st.error(f"Error fetching data: {e}")
        articles = []

# Pagination for articles
articles_per_page = 5
total_pages = (len(articles) // articles_per_page) + 1
if 'page_number' not in st.session_state:
    st.session_state.page_number = 1

start_idx = (st.session_state.page_number - 1) * articles_per_page
end_idx = start_idx + articles_per_page

# Navigation Buttons for Previous and Next Page
col1, col2, col3 = st.columns([1, 6, 1])

with col1:
    if st.session_state.page_number > 1:
        if st.button('Previous'):
            st.session_state.page_number -= 1

with col3:
    if st.session_state.page_number < total_pages:
        if st.button('Next'):
            st.session_state.page_number += 1

# Displaying the articles
if articles:
    for article in articles[start_idx:end_idx]:
        st.subheader(article["title"])
        st.write(article["description"] if article.get("description") else "No description available")
        st.write(f"**Source**: {article['source']['name']}")

        if article.get("urlToImage"):
            st.image(article["urlToImage"], caption=article["title"], use_container_width=True)

        st.write(f"[Read more]({article['url']})")

        # Sentiment Analysis
        description = article.get("description", "")
        if description:
            sentiment = TextBlob(description).sentiment.polarity
            if sentiment > 0:
                sentiment_text = "Positive"
            elif sentiment < 0:
                sentiment_text = "Negative"
            else:
                sentiment_text = "Neutral"
            st.write(f"Sentiment: {sentiment_text}")
        else:
            st.write("Sentiment: No description available for analysis.")

        st.write("---")
else:
    st.write("No articles found for the selected criteria.")

# Weather Updates Integration
weather_api_key = "61fa3c846bfc5626907351b8e31a4b84"
st.subheader("Weather Updates")
city = st.text_input("Enter your city for weather update")
if city:
    weather_url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={weather_api_key}&units=metric"
    weather_response = requests.get(weather_url).json()
    if weather_response.get("cod") == 200:
        weather_desc = weather_response['weather'][0]['description']
        temp = weather_response['main']['temp']
        st.write(f"Weather in {city}: {weather_desc.capitalize()}, {temp}°C")
    else:
        st.error("City not found.")

# Trending News Section
st.subheader("Trending News")
trending_news_url = f"{BASE_URL}?country=us&apiKey={API_KEY}&pageSize=10"
trending_response = requests.get(trending_news_url)
trending_articles = trending_response.json().get("articles", [])

for article in trending_articles:
    st.write(f"**{article['title']}**")
    st.write(article["description"])
    st.write(f"[Read more]({article['url']})")

# Dark Mode Toggle
dark_mode = st.checkbox("Enable Dark Mode", value=False)
if dark_mode:
    st.markdown("""
    <style>
    .stApp {background-color: #2e2e2e; color: white;}
    </style>
    """, unsafe_allow_html=True)

# Footer Section
st.markdown(
    """
    <div class="footer">
        &#169; 2024 Daily Update Hub. All rights reserved. | Designed by Prem Mishra
    </div>
    """,
    unsafe_allow_html=True
)
