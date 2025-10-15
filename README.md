# BIPM2025

import folium
from pyproj import Geod

geod = Geod(ellps="WGS84")

# Define stops
stops = [
    {"city": "Berlin, Germany", "lat": 52.5200, "lon": 13.4050, "popup": "Born & Raised"},
    {"city": "Bijeljina, Bosnia", "lat": 44.7587, "lon": 19.2144, "popup": "Homeland"}
]

# Create map
m = folium.Map(
    location=[40, 0],
    zoom_start=2.3,
    tiles="https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png",
    attr="© OpenStreetMap contributors © CARTO"
)

# Add markers
for stop in stops:
    folium.Marker(
        [stop["lat"], stop["lon"]],
        popup=stop["popup"],
        tooltip=stop["city"],
        icon=folium.Icon(color="blue", icon="info-sign")
    ).add_to(m)

# Function to compute many intermediate great-circle points
def great_circle_points(lat1, lon1, lat2, lon2, npts=100):
    points = geod.npts(lon1, lat1, lon2, lat2, npts)
    coords = [(lat1, lon1)] + [(lat, lon) for lon, lat in points] + [(lat2, lon2)]
    return coords

# Draw arcs between consecutive stops
for i in range(len(stops) - 1):
    start, end = stops[i], stops[i + 1]
    arc = great_circle_points(start["lat"], start["lon"], end["lat"], end["lon"], npts=60)
    folium.PolyLine(
        arc,
        color="darkblue",
        weight=3,
        opacity=0.7
    ).add_to(m)

# Save and open
m.save("academic_journey_map.html")
import webbrowser; webbrowser.open("academic_journey_map.html")
