import logging
import azure.functions as func
import json

def main(event: func.EventHubEvent):
    body = json.loads(event.get_body().decode('utf-8'))
    
    # Extracting the advanced GPS data points
    speed = body.get('speed')
    altitude = body.get('alt')
    satellites = body.get('sats')

    if speed > 80.0:
        logging.warning(f"SPEED ALERT: Vehicle traveling at {speed} km/h at altitude {altitude}m.")
    else:
        logging.info(f"Telemetry Received: Speed={speed}km/h, Satellites={satellites}")
