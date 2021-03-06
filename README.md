# cntk-faster-rcnn for node.js
A node wrapper a CNTK Faster-RCNN model

This module is based on the work done by [Nadav Bar](https://github.com/nadavbar/node-cntk-fastrcnn/) the evaluation script has been updated to support cntk faster-rcnn 

### Installation
Install by running: 
```
npm install cntk-fastercnn
```

## Python Preliminaries

Since the FRCNN detector uses bits of the CNTK Fast-RCNN implementation it has the same requirements as the CNTK
Faster-RCNN training pipeline. 

Before running the code in this repository, please make sure to install the required python packages as described
in <a href="https://docs.microsoft.com/en-us/cognitive-toolkit/object-detection-using-faster-r-cnn#setup">the Fast-RCNN CNTK tutorial</a>.  


### How to use

First, load the module and create an instance of a CNTKFRCNNModel object.

The constructor accepts an options object with the following fields:
 - cntkModelPath : Path to the CNTK Faster-RCNN model file.
 - cntkPath : The directory in which CNTK is installed. Default value: 'C:\local\cntk'.
 - cntkEnv : The CNTK env to use (e.g. 'cntk-py34', or 'cntk-py35'). If not specified, the latest available version is used.
 - verbose : if set - the module will write verbose output when running evaluation. Default: false.
 

For example:

```javascript
const CNTKFRCNNModel = require('cntk-fastercnn').CNTKFRCNNModel;

const modelFileLocation = 'C:\\cntk_model\\Faster-RCNN.model';

model = new CNTKFRCNNModel({cntkModelPath : modelFileLocation});
```

Next, call the model for detection using the **evaluateDirectory** method.
Calling the function will result in running the model over every .jpg image in the given directory.

The function accepts the following parameters:
- A path for a directory with images that will be used as input
- A completion callback that accepts an **error** object as the first parameter and the **result** of the detection operation as the second parameter. 

For example:

```javascript
model.evaluateDirectory('c:\\cntk_input\\testImages', (err, res) => {
    if (err) {
        console.info(err)
        return;
    }
    console.info('%j', res);
});
```

The result object of the detection operation will contain the possible classes for the detected objects (with class names and class numeric ids), and for each image in the directory it will contain the list of regions that were detected. Each region will have its boundaries and detected class.

Here is an example of the result object of a directory that contains 2 images (named '1.jpg' and '2.jpg'):
```json
{
	"frames": {
		"1.jpg": {
			"regions": [
				{
					"class": 1,
					"x1": 418,
					"x2": 538,
					"y2": 179,
					"y1": 59
				}
			]
		},
		"2.jpg": {
			"regions": [
				{
					"class": 2,
					"x1": 478,
					"x2": 597,
					"y2": 298,
					"y1": 59
				}
			]
		}
	},
	"classes": {
		"background" : 0,
		"human": 1,
		"cat": 2,
		"dog" : 3
	}
}
```

### Adding descriptive classes names
To add descriptive names copy the class_map.txt file from the dataset folder where you trained your FasterRCNN model into the same directory as your model file.
