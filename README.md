# serverless-bedrock-image-editor

# 

# A fully serverless AWS application that performs AI image editing (inpainting/outpainting/modification) using Amazon Bedrock, AWS Lambda, API Gateway, and S3.

# This project exposes a simple HTTP endpoint that accepts a prompt + optional mask image and returns results directly from Bedrock’s image models (Titan v2, Stable Diffusion, etc.).

# 

# This repository is ideal for developers building AI-powered tools such as image editors, photo cleanup utilities, background removal apps, or generative augmentation systems.

# 

# \## Features

# 

# 🖼 AI Image Editing (inpainting, outpainting, prompt-based editing)

# 

# ⚡ Fully Serverless — no servers to manage

# 

# 🔐 JWT-based Authorization (Cognito or any auth provider)

# 

# 🚀 Production-grade logging for debugging API failures

# 

# 🌐 CORS-compliant API Gateway setup

# 

# 🪣 Optional S3 storage for inputs/outputs

# 

# 📦 Easy to test using Python or curl

# 

# Architecture

# Browser / Client

# &nbsp;      │

# &nbsp;      ▼

# API Gateway  ──>  Lambda  ──>  Amazon Bedrock (Image Models)

# &nbsp;      │

# &nbsp;      └───── CORS + Auth

# 

# Example Request (Python)

# 

# This Python snippet replicates exactly what your browser does and gives full debug logs, helping you compare behavior.

# 

# import requests

# import json

# 

# API\_URL = "https://wsd8gmfbf2.execute-api.us-east-1.amazonaws.com/dev"

# JWT\_TOKEN = "<YOUR\_JWT\_TOKEN>"

# 

# headers = {

# &nbsp;   "Content-Type": "application/json",

# &nbsp;   "Authorization": JWT\_TOKEN

# }

# 

# payload = {

# &nbsp;   "prompt": {

# &nbsp;       "text": "Clear driveway",

# &nbsp;       "mode": "INPAINTING"

# &nbsp;   },

# &nbsp;   "mask": "<BASE64\_MASK\_IMAGE>"

# }

# 

# print("=== Sending POST request to API ===")

# resp = requests.post(API\_URL, headers=headers, data=json.dumps(payload))

# 

# print("Status Code:", resp.status\_code)

# print("Response Headers:")

# for k, v in resp.headers.items():

# &nbsp;   print(" ", k + ":", v)

# 

# try:

# &nbsp;   print("Response JSON:")

# &nbsp;   print(resp.json())

# except:

# &nbsp;   print("Raw Response:")

# &nbsp;   print(resp.text)

# 

# Example curl Command

# curl -X POST "https://wsd8gmfbf2.execute-api.us-east-1.amazonaws.com/dev" \\

# &nbsp; -H "Content-Type: application/json" \\

# &nbsp; -H "Authorization: <YOUR\_JWT\_TOKEN>" \\

# &nbsp; -d '{

# &nbsp;   "prompt": {

# &nbsp;     "text": "Clear driveway",

# &nbsp;     "mode": "INPAINTING"

# &nbsp;   },

# &nbsp;   "mask": "<BASE64\_MASK\_IMAGE>"

# &nbsp; }'

# 

# 

# Lambda Handler (Python – Extract from your working version)

# import json

# import boto3

# 

# client = boto3.client("bedrock-runtime")

# 

# def lambda\_handler(event, context):

# &nbsp;   print("Request event:", event)

# 

# &nbsp;   body = json.loads(event.get("body", "{}"))

# &nbsp;   prompt = body.get("prompt", {})

# &nbsp;   mask = body.get("mask")

# 

# &nbsp;   response = client.invoke\_model(

# &nbsp;       modelId="amazon.titan-image-generator-v2:0",

# &nbsp;       body=json.dumps({

# &nbsp;           "taskType": prompt.get("mode", "INPAINTING"),

# &nbsp;           "text": prompt.get("text"),

# &nbsp;           "maskImage": mask,

# &nbsp;           "imageGenerationConfig": { "cfgScale": 5 }

# &nbsp;       })

# &nbsp;   )

# 

# &nbsp;   result = json.loads(response\["body"].read())

# &nbsp;   return {

# &nbsp;       "statusCode": 200,

# &nbsp;       "headers": { "Content-Type": "application/json" },

# &nbsp;       "body": json.dumps(result)

# &nbsp;   }

# 

# Authentication

# 

# The API expects a JWT token in:

# 

# Authorization: <JWT>

# 

# 

# Supports:

# 

# Amazon Cognito

# 

# Auth0

# 

# Custom JWT providers

