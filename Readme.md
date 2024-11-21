# Edge Device LLM Compression Competition - Track 2

EdgeDeviceLLMCompetition track 2 final submission

## Submission Checklist

### The configuration and checkpoints of the original HuggingFace model 

Model: [p2o6e100/nGPT_800m · Hugging Face] https://huggingface.co/p2o6e100/nGPT_800m/tree/main

### Code for converting the model to MLC

google drive link

### Converted MLC model files

[google drive link](https://drive.google.com/drive/folders/1RIcObuvfWFdLEhL1pTOLVfVCGZjkw5sM?usp=sharing)

### CSV file

Check Result.csv

## Compilation Instruction


```shell
MODEL_NAME=ngpt

MODEL_TYPE=ngpt

mlc_llm convert_weight --model-type ${MODEL_TYPE} ./dist/models/${MODEL_NAME}/ --quantization q0f16 -o dist/$MODEL_NAME/ 

mlc_llm gen_config --model-type ${MODEL_TYPE} ./dist/models/${MODEL_NAME}/ --quantization q0f16 --conv-template llama3_1 --prefill-chunk-size 768 --context-window-size 768 --max-batch-size 1 -o dist/${MODEL_NAME}/
```

After gen_config, go to `.dist/models/decomp_llama/mlc-chat-config.json` and set `"system_prefix_token_ids"` under `"conv_template"` as bos_token_id, `"stop_token_ids"` as eos_token_id and set `"stop_str"` as `[]`

Then, compile the model

```shell
mlc_llm compile --model-type ${MODEL_TYPE} dist/${MODEL_NAME}/mlc-chat-config.json --device android -o ./dist/libs/${MODEL_NAME}-android.tar
```

### App Compile

go to ./cpp/serve/config.h and change default max_tokens from -1 to 128

go to ./android/MLCChat, replace mlc-package-config.json with the one we provided

```shell
mlc_llm package
```

### APK Generation

Use android studio Build/Generation Signed App Bundle/APK to generate apk

### Bundle Weight

use python bundle_weight.py --apk-path app/release/app-release-signed.apk to transfer weight from computer to mobile phone

Our `bundle_weight.py` is the same as the original one so either script works. Our `bundle_weight.py` can be found in `assets` folder
