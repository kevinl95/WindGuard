# WindGuard

<div align="center">
  <img src="assets/logo.png" alt="WindGuard Logo" width="200"/>
</div>

[![Lint CloudFormation Template](https://github.com/kevinl95/WindGuard/actions/workflows/main.yml/badge.svg)](https://github.com/kevinl95/WindGuard/actions/workflows/main.yml)

Automatically protect your inflatable decorations from wind damage by controlling a TP-Link Kasa smart plug based on real-time wind speed data.

[![Deploy to AWS](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=WindGuard&templateURL=https://your-bucket.s3.amazonaws.com/windguard/cloudformation.yml)

## Overview

WindGuard monitors local wind conditions using the [OpenWeatherMap](https://openweathermap.org) API and automatically turns your TP-Link Kasa outdoor smart plug on or off to protect inflatable decorations. The system uses configurable wind speed thresholds and operates only during scheduled hours.

## Features

- **Smart wind monitoring**: Uses OpenWeatherMap API for real-time local weather data
- **Configurable thresholds**: Set different wind speeds for turning decorations off and back on
- **Time-based operation**: Only active during specified hours (default: 5 PM - 10 PM)
- **Secure credential storage**: TP-Link and API credentials stored in AWS Secrets Manager
- **SMS/Email notifications**: Optional alerts when the plug state changes
- **Serverless architecture**: Cost-effective with AWS Lambda, triggered every 5 minutes
- **Easy deployment**: Single CloudFormation template

## Prerequisites

1. **TP-Link Kasa Account**: Set up a Kasa account and connect your outdoor smart plug
2. **OpenWeatherMap API Key**: Sign up at [OpenWeatherMap](https://openweathermap.org/api) for a free API key
3. **AWS Account**: Required for deploying the CloudFormation stack

## Quick Start

### 1. Deploy the Stack

Click the "Launch Stack" button above and fill in the required parameters.

### 2. Required Parameters

- **TPLinkEmail**: Your TP-Link Kasa account email
- **TPLinkPassword**: Your TP-Link Kasa account password
- **TPLinkDeviceAlias**: The friendly name of your smart plug in the Kasa app
- **OpenWeatherApiKey**: Your OpenWeatherMap API key
- **Latitude**: Latitude of your location (decimal degrees)
- **Longitude**: Longitude of your location (decimal degrees)

### 3. Optional Parameters

- **WindOffThreshold**: Wind speed (mph) to turn off plug (default: 20)
- **WindOnThreshold**: Wind speed (mph) to turn on plug (default: 10)
- **OnStartHour**: Hour to start monitoring (24h format, default: 17)
- **OnEndHour**: Hour to stop monitoring (24h format, default: 22)
- **TimeZone**: Your timezone (default: America/New_York)
- **NotifyEmail**: Email for notifications (optional)

## How It Works

1. **Weather Monitoring**: Every 5 minutes, the Lambda function fetches current wind speed from OpenWeatherMap
2. **Schedule Check**: Verifies if current time is within the configured operating hours
3. **Threshold Evaluation**:
   - If wind speed exceeds the "off" threshold during operating hours: turns plug OFF
   - If wind speed drops below the "on" threshold during operating hours: turns plug ON
4. **Notifications**: Sends SNS notifications when plug state changes
5. **Device Control**: Uses TP-Link Kasa Cloud API to control the smart plug

## Architecture

- **AWS Lambda**: Serverless function handling weather checks and device control
- **AWS Secrets Manager**: Secure storage for TP-Link credentials and API keys
- **Amazon EventBridge**: Triggers Lambda function every 5 minutes
- **Amazon SNS**: Sends notifications when plug state changes
- **AWS IAM**: Least-privilege permissions for Lambda execution

## Cost

WindGuard is designed to be cost-effective:
- **Lambda**: ~8,640 invocations/month (free tier covers 1M requests) = FREE
- **Secrets Manager**: Credential storage = ~$0.40/month
- **DynamoDB**: State tracking (~8,640 reads, ~100 writes/month) = ~$0.01/month
- **SNS**: Notifications (50-100 messages/month) = ~$0.0001/month
- **EventBridge**: Standard rule = FREE

**Total estimated cost: ~$0.41/month (~$5/year)**

This is less than the cost of a single coffee for a full year of automated decoration protection!

## Troubleshooting

### Common Issues

1. **Device not found**: Ensure the TPLinkDeviceAlias exactly matches the name in your Kasa app
2. **Weather API errors**: Verify your OpenWeatherMap API key is valid and active
3. **No notifications**: Check that your email is confirmed in the SNS subscription

### Logs

View Lambda logs in CloudWatch:
```bash
aws logs tail /aws/lambda/WindGuard-WindGuardFunction-XXXXX --follow
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.