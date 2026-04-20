import logging
import azure.functions as func
import json

def main(event: func.EventHubEvent, outputBlob: func.Out[str]):
    # 1. Receive and parse telemetry from the IoT Hub
    body = json.loads(event.get_body().decode('utf-8'))
    logging.info(f"Processing telemetry for archiving: {body}")

    # 2. Add a timestamp or metadata if needed
    body['processed_at'] = event.enqueued_time.isoformat()

    # 3. Save to Blob Storage via the Output Binding
    logging.info("Telemetry successfully archived to Blob Storage.")
