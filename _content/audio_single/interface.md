---
Audio streaming interface specification
---
Audio streaming between the single-tenant audio gateway and the audio client uses a web socket interface.

Important:  Use only if you have IBM Watson Assistant for Hospitality

#### Communication Messages
Messages are used on the web socket interface to communicate between the audio client and the audio gateway.  All messages are in JSON format.

 Each message includes a transaction ID that is used by the audio gateway to track the transaction.  To ensure that each ID is unique, consider using a globally unique identifier (GUID) for the transaction ID.

#### Audio start
Audio start messages are sent between the audio client and the audio gateway.  The message indicates that the sender is ready to send audio data.  You can optionally specify which speech-to-text (stt) and text-to-speech option (tts) to use for the transaction.  If included, the `stt` and `tts` values override the default settings.

Example:
```
{
  "id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
  "action": "audio_start",
  "options": {
    "stt": {},
    "tts": {}
  }
}
```
#### Audio data
Audio data messages are sent between the audio client and the audio gateway. The message is used to send audio data in binary format.  Use the data parameter to specify the content of the binary data in a string format.  Use the encoding parameter to specify the type of encoding to use, for example, base64. The message is always preceded by an audio start message.

Example:
```
{
"id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
"action": "audio_data",
"data": {},
"encoding": "base64"
}
```
#### Audio data adaptive streaming
Audio data adaptive streaming messages are sent between the audio client and the audio gateway.  The message is used to apply an adaptive streaming format to the audio to improve audio quality.  Otherwise, the audio might sound choppy or it might not be possible to deliver the audio message before the playback of the audio exceeds the audio buffer.

#### Audio end
Audio end messages are sent between the audio client and the audio gateway.   The message indicates that the sender has completed the sending of audio data. The message is always preceded by an audio start message.

Example (from audio client):
```
{
  "id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
  "action": "audio_end",
}
```

Example (from audio gateway):
```
{
  "id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
  "action": "audio_end",
  "prompt": (true|false)
}
```
To specify that that audio gateway expects a response from the audio client, set `prompt` to `true`.  When set to true, if the device supports it, the audio device accepts the user input without first requiring a wake up word or some other action.

#### Speech-to-text options
Speech-to-text options messages are sent from the audio client to the audio gateway.  The message specifies which speech-to-text engine to use for the current transaction.

Example:
```
{
  "id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
  "action": "stt_options",
  "options": {
    "engine": "watson",
    "content_type": "audio/l16; rate=16000; channels=1",
    "inactivity_timeout": -1,
    "smart_formatting": true
  }
}
```
Note:  You can override which speech-to-text option to use in the audio start message.

#### Text-to-speech options
Text-to-speech options messages are sent from the audio client to the audio gateway.  The message specifies which text to speech engine to use for the current transaction.

Example:
```
{
  "id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
  "action": "tts_options",
  "options": {
    "engine": "watson",
    "accept": "audio/l16; rate=16000; channels=1",
    "voice": "en-US_LisaVoice"
  }
}
```
Note:  You can override which text-to-speech option to use in the audio start message.

#### Text
Text messages are sent between the audio client and the audio gateway.  The audio client can send a text message to the audio gateway.  The audio gateway can use a text message to deliver a textual response.

Example:
```
{
  "id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
  "action": "text",
  "text": "Hello world"
}
```

#### Location
Location message are sent from the audio client to the audio gateway.  The messages can be used to provide the current location of the audio device.

Example:
```
{
  "id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
  "action": "location",
  "location": {
    "latitude": 45.408032,
    "longitude": -123.0083655
  }
}
```
Alternatively, you might use a configuration parameter to determine the location of the audio device. If a configuration parameter is used, the location message can be ignored.

#### Response
A response message is sent from the audio gateway to the audio client.  The data parameters includes the response  from the skill that processed the request.

Example:
```
{
  "id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
  "action": "response",
  "data": {}
}
```
#### Speech-to-text transcript
The speech-to-text transcript message is sent from the audio gateway to the client.  The message provides a transcript of the audio response in text format.  The  ID parameter matches the transaction ID in the audio start message.  The transactionID parameter is used for reporting errors in the transcript.

Example:
```
{
  "id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
  "action": "stt_transcript",
  "transcript": "hello world",
  "confidence": 0.987654321,
  "transactionId" "abcdefg"
}
```
#### Error
Error messages are sent from the audio gatewawy to the audio client  when an error occurs.  The ID paramemter matches the transaction ID of the transaction which caused the error.

Example:
```
{
  "id": "86836b60-0a5f-11e7-aa14-a3b192c91ac9",
  "action": "error",
  "error": {}
}
```

### IoT control REST Interface

#### Converse
Send an utterance or a command to Watson Assistant Solutions for processing.

Use the converse endpoint:
`{host}/converse [POST]`
Request example:
`{
  "text": "string"
}`
Response example:
`{
  "response": "string",
  "shouldEndSession": true,
  "responseTime": "string",
  "expressiveness": "string",
  "expertise": "string"
}`

#### Administering devices and device controllers
A REST API is provided for administering client devices and device controllers.

To list client devices, use the clients endpoint:
`{host}/iot/clients [GET]`

To create client devices, use the clients endpoint:
`{host}/iot/clients [POST]`

To delete a client device, use the clients endpoint and pass the client ID:
`{host}/iot/clients/{id} [DELETE]`

To list the details of a client device use the cleints endpoint and pass the client ID:
`{host}/iot/clients/{id} [GET]`

To update a client device, use the clients endpoint and pass the client ID:
`{host}/iot/clients/{id} [PUT]`

To count the number of device controllers, use the
countdevices endpoint:
`{host}/iot/countdevices [GET]`

To list device controllers, use the devices endpoint:
`{host}/iot/devices [GET]`

To create a device contoller, use the devices endpoint:
`{host}/iot/devices [POST]`
Example:
`{
  "id": "string",
  "deviceType": "string",
  "authToken": "string",
  "clients": [
    "string"
  ]
}`

To delete a device controller, use the devices endpoint and pass the device controller ID:
`{host}/iot/devices/{id} [DELETE]`

To update a device controller, use the devices endpoint and pass the device controller ID:
`{host}/iot/devices/{id} [PUT`
Example:
`{
  "id": "string",
  "deviceType": "string",
  "authToken": "string",
  "clients": [
    "string"
  ]
}`

##### View logs
To view log files, use the logs endpoint:
`{host}/logs [GET]`
Log data is grouped by transaction ID.