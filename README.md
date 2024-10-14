# Redbus

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.keys import Keys
from selenium.common.exceptions import StaleElementReferenceException, TimeoutException
from selenium.webdriver.common.action_chains import ActionChains
import time

# Open the web browser
driver = webdriver.Chrome()
driver.get("https://www.redbus.in/online-booking/ksrtc-kerala/?utm_source=rtchometile")

# Wait for the page to load
wait = WebDriverWait(driver, 10)

driver.maximize_window() # Set the maximum window size

def extract_route_links():
    route_links = []
    routes = driver.find_elements(By.XPATH, '//a[@class="route"]')
    for route in routes:
        route_links.append(route.get_attribute('href'))
    return route_links

# Wait for the page to load
wait = WebDriverWait(driver, 60)

# Extract links from the first page
all_route_links = extract_route_links()

for page_number in range(1, 3):   

    if page_number <2:
    #func_call
        try:
            pg_container = wait.until(EC.presence_of_element_located((By.XPATH, '//div[@class="D117_main D117_container"]')))

            # Find the active page number element
            active_page_number = pg_container.find_element(By.XPATH, '//div[contains(@class, "DC_117_pageTabs DC_117_pageActive")]')

            # Find the next page number element
            next_page_number = int(active_page_number.text) + 1

            # Find the next page button element
            next_page_button = pg_container.find_element(By.XPATH, f'//div[contains(@class, "DC_117_pageTabs") and text()="{str(next_page_number)}"]')

            # Click the next page button
            driver.execute_script("arguments[0].scrollIntoView();", next_page_button)
            time.sleep(10)
            next_page_button.click()
            print('successs')
            all_route_links.extend(extract_route_links())
        except TimeoutException:
            print(f'Failed to load page {page_number + 1}')

        wait = WebDriverWait(driver, 20)

Kerala_Bus_Details = []

for link in all_route_links:
    driver.get(link)
    # Set the maximum window size
    driver.maximize_window() 

    time.sleep(20)

    # Clicking view buses
    #wait = WebDriverWait(driver, 20)
    view_bus_buttons = driver.find_elements(By.XPATH, '//div[@class="button"]')

    for i, view_bus_button in enumerate(view_bus_buttons):
        try:
            time.sleep(20)
            ActionChains(driver).move_to_element(view_bus_button).click().perform()
        except TimeoutException:
            print("Timeout occurred while waiting for the elements")

    time.sleep(10)

    # Scrolling pages
    while True:
        # Get the current scroll position
        current_position = driver.execute_script("return window.pageYOffset;")

        # Scroll to the bottom of the page
        driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
        
        # Wait for the page to load after scrolling
        wait = WebDriverWait(driver, 10)
        wait.until(lambda driver: driver.execute_script("return document.readyState") == "complete")

        # Get the new scroll position
        new_position = driver.execute_script("return window.pageYOffset;")

        # Check if the scroll position has changed
        if current_position == new_position:
            break

    
    route_name = driver.find_element(By.XPATH, '//*[@id="mBWrapper"]/section/div[2]/h1').text
    routes = driver.current_url
    time.sleep(20)
    bus_elements = driver.find_elements(By.XPATH, '//div[@class="clearfix bus-item"]')
    for bus_element in bus_elements:
        try:
            bus_name = bus_element.find_element(By.XPATH, '//div[@class="travels lh-24 f-bold d-color"]').text
            bus_type = bus_element.find_element(By.XPATH, '//div[@class="bus-type f-12 m-top-16 l-color evBus"]').text
            bus_departure_time = bus_element.find_element(By.XPATH, '//div[@class="dp-time f-19 d-color f-bold"]').text   
            bus_arrival_time = bus_element.find_element(By.XPATH, '//div[@class="bp-time f-19 d-color disp-Inline"]').text
            bus_duration = bus_element.find_element(By.XPATH, '//div[@class="dur l-color lh-24"]').text
            bus_fare = bus_element.find_element(By.XPATH, '//div[@class="fare d-block"]').text
            seat_availability = bus_element.find_element(By.XPATH, '//div[@class="column-eight w-15 fl"]').text
            review_element = bus_element.find_element(By.XPATH, '//div[@class="column-six p-right-10 w-10 fl"]').text

            # Add bus details to the list
            Kerala_Bus_Details.append({
                "Route Name": route_name,
                "Route Link": routes,
                "Bus Name": bus_name,
                "Bus Type": bus_type,
                "Bus Departure Time": bus_departure_time,
                "Bus Arrival Time": bus_arrival_time,
                "Bus Duration": bus_duration,
                "Bus Fare": bus_fare,
                "Seat Availability": seat_availability,
                "Review": review_element
            })
        except StaleElementReferenceException:
            print("StaleElementReferenceException occurred while extracting bus details.")

        wait = WebDriverWait(driver, 10)

        for bus_details in Kerala_Bus_Details:
            print(f"Route Name: {bus_details['Route Name']}")
            print(f"Route Link: {bus_details['Route Link']}")
            print(f"  Bus Name: {bus_details['Bus Name']}")
            print(f"  Bus Type: {bus_details['Bus Type']}")
            print(f"  Departure Time: {bus_details['Bus Departure Time']}")
            print(f"  Arrival Time: {bus_details['Bus Arrival Time']}")
            print(f"  Duration: {bus_details['Bus Duration']}")
            print(f"  Bus Fare: {bus_details['Bus Fare']}")
            print(f"  Seat Availability: {bus_details['Seat Availability']}")
            print(f"  Review: {bus_details['Review']}")
            print("")

wait = WebDriverWait(driver, 30)
driver.quit()
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.keys import Keys
from selenium.common.exceptions import StaleElementReferenceException, TimeoutException
from selenium.webdriver.common.action_chains import ActionChains
import time

# Open the web browser
driver = webdriver.Chrome()
driver.get("https://www.redbus.in/online-booking/ksrtc-kerala/?utm_source=rtchometile")

# Wait for the page to load
wait = WebDriverWait(driver, 10)

driver.maximize_window() # Set the maximum window size

def extract_route_links():
    route_links = []
    routes = driver.find_elements(By.XPATH, '//a[@class="route"]')
    for route in routes:
        route_links.append(route.get_attribute('href'))
    return route_links

# Wait for the page to load
wait = WebDriverWait(driver, 60)

# Extract links from the first page
all_route_links = extract_route_links()

for page_number in range(1, 3):   

    if page_number <2:
    #func_call
        try:
            pg_container = wait.until(EC.presence_of_element_located((By.XPATH, '//div[@class="D117_main D117_container"]')))

            # Find the active page number element
            active_page_number = pg_container.find_element(By.XPATH, '//div[contains(@class, "DC_117_pageTabs DC_117_pageActive")]')

            # Find the next page number element
            next_page_number = int(active_page_number.text) + 1

            # Find the next page button element
            next_page_button = pg_container.find_element(By.XPATH, f'//div[contains(@class, "DC_117_pageTabs") and text()="{str(next_page_number)}"]')

            # Click the next page button
            driver.execute_script("arguments[0].scrollIntoView();", next_page_button)
            time.sleep(10)
            next_page_button.click()
            print('successs')
            all_route_links.extend(extract_route_links())
        except TimeoutException:
            print(f'Failed to load page {page_number + 1}')

        wait = WebDriverWait(driver, 20)

Kerala_Bus_Details = []

for link in all_route_links:
    driver.get(link)
    # Set the maximum window size
    driver.maximize_window() 

    time.sleep(20)

    # Clicking view buses
    #wait = WebDriverWait(driver, 20)
    view_bus_buttons = driver.find_elements(By.XPATH, '//div[@class="button"]')

    for i, view_bus_button in enumerate(view_bus_buttons):
        try:
            time.sleep(20)
            ActionChains(driver).move_to_element(view_bus_button).click().perform()
        except TimeoutException:
            print("Timeout occurred while waiting for the elements")

    time.sleep(10)

    # Scrolling pages
    x=0
    while True:
        x=x+1
        driver.execute_script("window.scrollBy(0, 100);")

        if (x>1000):

            break

    
    route_name = driver.find_element(By.XPATH, '//*[@id="mBWrapper"]/section/div[2]/h1').text
    routes = driver.current_url
    time.sleep(20)
    bus_elements = driver.find_elements(By.XPATH, '//div[@class="clearfix row-one"]')

    for bus_element in bus_elements:
        try:
            bus_name = bus_element.find_element(By.XPATH, '//div[@class="travels lh-24 f-bold d-color"]').text
            bus_type = bus_element.find_element(By.XPATH, '//div[@class="bus-type f-12 m-top-16 l-color evBus"]').text
            bus_departure_time = bus_element.find_element(By.XPATH, '//div[@class="dp-time f-19 d-color f-bold"]').text   
            bus_arrival_time = bus_element.find_element(By.XPATH, '//div[@class="bp-time f-19 d-color disp-Inline"]').text
            bus_duration = bus_element.find_element(By.XPATH, '//div[@class="dur l-color lh-24"]').text
            bus_fare = bus_element.find_element(By.XPATH, '//div[@class="fare d-block"]').text
            seat_availability = bus_element.find_element(By.XPATH, '//div[@class="column-eight w-15 fl"]').text
            review_element = bus_element.find_element(By.XPATH, '//div[@class="column-six p-right-10 w-10 fl"]').text

            # Add bus details to the list
            Kerala_Bus_Details.append({
                "Route Name": route_name,
                "Route Link": routes,
                "Bus Name": bus_name,
                "Bus Type": bus_type,
                "Bus Departure Time": bus_departure_time,
                "Bus Arrival Time": bus_arrival_time,
                "Bus Duration": bus_duration,
                "Bus Fare": bus_fare,
                "Seat Availability": seat_availability,
                "Review": review_element
            })
        except StaleElementReferenceException:
            print("StaleElementReferenceException occurred while extracting bus details.")

        wait = WebDriverWait(driver, 10)

        for bus_details in Kerala_Bus_Details:
            print(f"Route Name: {bus_details['Route Name']}")
            print(f"Route Link: {bus_details['Route Link']}")
            print(f"  Bus Name: {bus_details['Bus Name']}")
            print(f"  Bus Type: {bus_details['Bus Type']}")
            print(f"  Departure Time: {bus_details['Bus Departure Time']}")
            print(f"  Arrival Time: {bus_details['Bus Arrival Time']}")
            print(f"  Duration: {bus_details['Bus Duration']}")
            print(f"  Bus Fare: {bus_details['Bus Fare']}")
            print(f"  Seat Availability: {bus_details['Seat Availability']}")
            print(f"  Review: {bus_details['Review']}")
            print("")

wait = WebDriverWait(driver, 20)
driver.quit()
