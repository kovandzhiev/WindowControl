# WindowControl
An Arduino project which will open and close the window in the room

## The logic
1. WiFi connection is waiting for 120 seconds before switch to Access point

1. Base topic. Use it for broadcast. See first callback.
Device name. Example: room_name.

1. Open, close the window. Operations duration are:
 - Close 25 sec.
 - Open  23 sec.

1. The first operation when the device has been started, will be close the window - set current state to 0.

1. Close the window. From security consideration this operation will take max close time (25 sec) + 5 sec = 30 sec. The command is: base_topic/device_name/window/set:0

1. In the feature will be implemented logic:
 - opened for more than (10 sec is time for normal going through the door) 20 sec publish topic /ventilate:on
 - closed for more than 20 sec publish topic /ventilate:off

1. Topics description
Structure: topic:[payload]

Callback includes external request ->, response from device <-
 - broadcast command
  -> base_topic:[null]
  <- base_topic/device_name:[ok]
 - ask device by name
  -> base_topic/device_name:[null]
  <- base_topic/device_name:[ok]
 - get device full information, respond with all available data
  -> base_topic/device_name:[all]
  <- all topics from Publish
 - get current window state
  -> base_topic/device_name/window:[null] - respond with current window state
  <- base_topic/device_name/window/set:[0 (Closed), 1, 2, 3, 4 (Full opened)]
 - get all gates state
  -> base_topic/device_name/gate:[null]
  <- base_topic/device_name/gate/1:[open | close] - open or close a window/door 1
  <- base_topic/device_name/gate/2:[open | close] - open or close a window/door 2
  <- base_topic/device_name/gate/3:[open | close] - open or close a window/door 3
  <- base_topic/device_name/gate/4:[open | close] - open or close a window/door 4

Publish include responses:
 - the device has just stated. This message has to send to a remote server to get current device settings.
  <- base_topic/device_name:[ready]
 - the window state
  <- base_topic/device_name/window:[0 (Closed), 1,  2, 3, 4 (Full opened)]
 - the gates state
  <- base_topic/device_name/gate/1:[open | close] - open or close a window/door 1
  <- base_topic/device_name/gate/2:[open | close] - open or close a window/door 2
  <- base_topic/device_name/gate/3:[open | close] - open or close a window/door 3
  <- base_topic/device_name/gate/4:[open | close] - open or close a window/door 4
 - in the future will be implemented
  <- base_topic/device_name/ventilate:[on | off] - open or close ventilation gates