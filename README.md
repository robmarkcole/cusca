# CUSCA - CUrrent event Streaming CamerA

This webapp generates an MJPEG Stream from an RTSP stream. The stream is based on filtered frames, with meaningful events (currently persons detected in the frame) that loop indefinentely until new events come in.

This webapp requires a Google Coral TPU.

# Install

- Install edgetpu libraries from https://coral.ai/docs/accelerator/get-started
- Download tflite from https://www.tensorflow.org/lite/guide/python - getting `ERROR: tflite_runtime-2.1.0.post1-cp37-cp37m-macosx_10_14_x86_64.whl is not a supported wheel on this platform.`, because I am on 10.15?
- Adjust requirements.txt (to match your OS)

# Running
Download the models with `sudo bash install_models.sh` then:

```(venv) python3 app```

# Configuration

All config options are based on ENV variables (easy to use with containers)

options available:

| Variable        | Information                                                             | Default                                                                     |
| --------------- | ----------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| CAMERA_URL      | RTSP stream                                                             | 'rtsp://admin@192.168.1.96:554/user=admin_password=_channel=0_stream=0.sdp' |
| MODEL_FILE      | Path to file containing model to be used                                | "models/ssd_mobilenet_v2_coco_quant_postprocess_edgetpu.tflite"             |
| LABELS_FILE     | Path to file containing labels to be used (matched with the model file) | "models/coco_labels.txt"                                                    |
| MQTT_SERVER     | MQTT Broker address                                                     | '192.168.1.100'                                                             |
| MQTT_BASE_TOPIC | Topic under which camera configuration and events are published         | 'cusca'                                                                     |
| BUFFER_SIZE     | Number of frames kept by the MJPEG stream                               | 24                                                                          |

Furthermore, some variables can be adjusted in realtime through MQTT such as:

| Topic                             | Information                                                                            | Default |
| --------------------------------- | -------------------------------------------------------------------------------------- | ------- |
| cusca/armed                       | If True frames are processed else cusca acts like a proxy                              | True    |
| cusca/mjpeg_fps                   | frames per second in the mjpeg stream                                                  | 4       |
| cusca/percentage_processed_frames | % or frames from the original rtsp - default 50%                                       | 0.5     |
| cusca/threshold                   | consider an event if probability higher then (0 to 1)                                  | 0.14    |
| cusca/buffer                      | Number of frames kept by the MJPEG stream                                              | 24      |
| cusca/status                      | (Read-only) Indication that CUSCA daemon is running or not                             | offline |
| cusca/event_detected              | (Read-only) Boolean indication if we are in the middle on an event                     | True    |
| cusca/event_probability           | (Read-only) Probability of event detection in the last frame that passed the threshold | -       |

## Docker
```
    docker build -t cusca .
    docker run -p 5000:5000 -e CAMERA='rtsp ...' cusca:latest
```

## Developers
* Create venv -> `$ python3 -m venv venv`
* Use venv -> `$ source venv/bin/activate`
* Install requirements -> `$ pip install -r requirements.txt`