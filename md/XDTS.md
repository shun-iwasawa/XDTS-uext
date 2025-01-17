## XDTS file format
 #### As of 2018/11/29
 Created and published by Celsys, Inc. in cooperation with TOEI ANIMATION Co.,Ltd.

 Note that this jsonc (JSON with Comments) file is based on the following document:
 - "XDTS File Format (November 29, 2018)"
 https://vd.clipstudio.net/clipcontent/paint/app/ToeiAnimation/XDTSFileFormat_en.pdf
 
 If there is a discrepancy with the original document, the original document is correct.


```json
// XDTS files are text data with a first line matching the "exchangeDigitalTimeSheet Save Data" 
// character string, and subsequent lines composed of JSON Schema defined JSON data.
{
    "$schema": "http://json-schema.org/draft-07/schema",
    "type": "object",
    "properties": {
        "header": {
            "description": "Cut / scene information",
            "type": "object",
            "properties": {
                "cut": {
                    "description": "Cut No.",
                    "type": "string",
                    "pattern": "\\d{1,4}"
                },
                "scene": {
                    "description": "Scene No.",
                    "type": "string",
                    "pattern": "\\d{1,4}"
                }
            },
            "required": ["cut", "scene"]
        },
        "timeTables": {
            "description": "Timesheet info",
            "type": "array",
            "minItems": 1,
            "items": {
                "type": "object",
                "properties": {
                    "fields": {
                        "description": "Individual field info",
                        "type": "array",
                        "items": {
                            "type": "object",
                            "properties": {
                                "fieldId": {
                                    "description": "Field type(*1)",
                                    "type": "integer",
                                    "enum": [0, 3, 5]
                                },
                                "tracks": {
                                    "description": "Individual field layer info",
                                    "type": "array",
                                    "items": {
                                        "type": "object",
                                        "properties": {
                                            "frames": {
                                                "description": "Individual layer frame information",
                                                "type": "array",
                                                "items": {
                                                    "type": "object",
                                                    "properties": {
                                                        "data": {
                                                            "description": "Frame instruction information",
                                                            "type": "array",
                                                            "items": {
                                                                "type": "object",
                                                                "properties": {
                                                                    "id": {
                                                                        "description": "Instruction type(*2)",
                                                                        "type": "integer",
                                                                        "enum": [0]
                                                                    },
                                                                    "values": {
                                                                        "description": "Instruction value(*3)",
                                                                        "type": "array",
                                                                        "minItems": 1,
                                                                        "items": {
                                                                            "type": "string"
                                                                        }
                                                                    }
                                                                },
                                                                "required": ["id", "values"]
                                                            }
                                                        },
                                                        "frame": {
                                                            "description": "Frame No.(*5)",
                                                            "type": "integer",
                                                            "minimum": 0
                                                        }
                                                    },
                                                    "required": ["data", "frame"]
                                                }
                                            },
                                            "trackNo": {
                                                "description": "Layer No.(*6)",
                                                "type": "integer",
                                                "minimum": 0
                                            }
                                        },
                                        "required": ["frames", "trackNo"]
                                    }
                                }
                            },
                            "required": ["fieldId", "tracks"]
                        }
                    },
                    "duration": {
                        "description": "Timesheet total frames",
                        "type": "integer",
                        "minimum": 1
                    },
                    "name": {
                        "description": "Timesheet name",
                        "type": "string"
                    },
                    "timeTableHeaders": {
                        "description": "Individual field layer name",
                        "type": "array",
                        "items": {
                            "type": "object",
                            "properties": {
                                "fieldId": {
                                    "description": "Field type(*1)",
                                    "type": "integer",
                                    "enum": [0, 3, 5]
                                },
                                "names": {
                                    "description": "Layer name array(*7)",
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                }
                            },
                            "required": ["fieldId", "names"]
                        }
                    }
                },
                "required": ["duration", "name", "timeTableHeaders"]
            }
        },
        "version": {
            "description": "XTDS file format version",
            "type": "integer",
            "enum": [5]
        }
    },
    "required": ["timeTables", "version"]
}
```

*1 Field: Input location of each timesheet instruction. Fields are divided into cells, dialog, and 
camerawork.

| fieldId | field name | details                                 |
| ------- | ---------- | --------------------------------------- |
|    0    |    Cell    | Field for cell numbers                  |
|    3    |   Dialog   | Field for speaker names and line timing |
|    5    | Camerawork | Field for camerawork instructions       |

*2 Currently only supports instructions with id=0.

*3 Values are different depending on the field type. Values for each field are as follows.

| Field name | value details | value element numbers |
| ---------- | ------------- | --------------------- |
|    Cell    | Enter the cell number string and special instruction string(*4) | 1 |
|   Dialog   | In the dialog's first frame, enter the speaker name in the first <br>element, and the dialog's string in the second element. When <br>the line lasts for multiple frames, specify the character string <br>"SYMBOL_HYPHEN" up to the end frame. | 1～2 |
| Camerawork | In the camerawork's first frame, enter the camerawork <br>instruction string When the camerawork lasts for multiple <br>frames, specify the character string "SYMBOL_HYPHEN" up to <br>the end frame. | 1 |

*4 Special instruction character strings

| Character string | Instruction                         | Valid field |
| ---------------- | ----------------------------------- | ----------- |
|  SYMBOL_TICK_1   | Inbetween symbol (○)                |    Cell     |
|  SYMBOL_TICK_2   | Reverse sheet symbol (●)            |    Cell     |
| SYMBOL_NULL_CELL | Empty cell symbol (×)               |    Cell     |
|  SYMBOL_HYPHEN   | Continue previous field instruction | All fields  |

*5 Set as 0 when specifying the first frame.

*6 Set as 0 for the bottom layer.

*7 Specify layer names with trackNo's that match numbers counted as 0,1,2, etc
