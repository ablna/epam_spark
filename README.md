# During data preparation, coordinates originally stored as text strings were converted to numerical floating-point values. This process uncovered one data entry with missing latitude and/or longitude information.

+-----------+------------+--------------+-----------------------+-------+------+----+----+
|         id|franchise_id|franchise_name|restaurant_franchise_id|country|  city| lat| lng|
+-----------+------------+--------------+-----------------------+-------+------+----+----+
|85899345920|           1|       Savoria|                  18952|     US|Dillon|NULL|NULL|
+-----------+------------+--------------+-----------------------+-------+------+----+----+

#The missing latitude and/or longitude values were successfully filled in using the OpenCage geocoding service. To ensure the accuracy of the generated coordinates, unit tests were implemented to verify that OpenCage accurately identifies latitude and longitude based on country and city names.

assert abs(fill_null_lat_lng('Kazakhstan', 'Pavlodar') - 52.287303) <= 1 # arbitrary known address
assert abs(fill_null_lat_lng('Kazakhstan', 'Pavlodar', 'lng') - 76.967402) <= 1  # arbitrary known address
assert abs(fill_null_lat_lng('Us', 'Rapid City', 'lat') - 44.080) <= 1  # address from dataset
assert abs(fill_null_lat_lng('Us', 'Rapid City', 'lng') - -103.250) <= 1  # address from datase

After that geohashes were computer for restaurants. Unit tests for them were as follows:

assert generate_geohash(52.287303, 76.967402) == 'v9q9' # an arbitrary example
assert generate_geohash(0, 0) == 's000' # 0 case
assert generate_geohash(-90, 90) == 'n000' # edge case
assert generate_geohash(34.578, -87.021) == 'dn4h' # an arbitrary case from data, checked manually


Weather data was integrated with the original dataset containing restaurant information. To prevent redundant data, a left join was performed, using restaurant data as the base and linking it with weather records. Before the join, weather data was consolidated to ensure a single temperature value represented each date and geohash combination. The resulting combined dataset was saved, partitioned by the wthr_date column to optimize retrieval and analysis based on weather dates. The final structure of the partitioned data is visually depicted in the image available at the provided link (https://freeimage.host/i/JRz5w9j). The specific code implementation for these data operations can be accessed at the GitHub repository: https://github.com/ablna/epam_spark.

