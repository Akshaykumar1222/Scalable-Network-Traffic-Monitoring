import json
import boto3

sns = boto3.client('sns')

def lambda_handler(event, context):
    suspicious_ips = []
    for record in event['Records']:
        log_data = json.loads(record['body'])
        if log_data.get("bytes", 0) > 1000000:  # Simple anomaly detection
            suspicious_ips.append(log_data.get("srcAddr"))

    if suspicious_ips:
        message = f"Suspicious traffic detected from IPs: {', '.join(suspicious_ips)}"
        sns.publish(
            TopicArn='arn:aws:sns:us-east-1:123456789012:TrafficAlerts',
            Message=message,
            Subject='Network Traffic Alert'
        )

    return {
        'statusCode': 200,
        'body': json.dumps('Processed network logs')
    }
