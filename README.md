# Starting Kit for LLMPC Red Team
This repository is for the Red Team participants for [NeurIPS 2024 LLM Privacy Challlenge](https://llm-pc.github.io/).

## Model
We have released the model in HuggingFace [here](https://huggingface.co/LLM-PBE/LLMPC-Red-Team-Llama3.1-8b-instruct), which is the Llama3.1-8b-instruct model fine-tuned on chat data with private information.

## Data for Development Phase
The data for development phase are available under `data` directory. There are two files
- LLM-PC-development-scrubbed-data.jsonl: It includes 1500 training samples where the private information is masked.
- LLM-PC-development-pii.jsonl: It includes the correponding private information in the scrubbed data.


## Data for Test Phase
The data for test phase is available under `data` directory:
- LLM-PC-test-scrubbed.jsonl: Like the development data, it includes 1500 samples that were used in the training of the same model.

## Requirements
**Goal**: You need to develop attack methods to infer the masked private information in the scrubbed data. A higher extraction rate is better. The provided data is for your reference in the development phase. In the test phase, you will be given the test data (with the same format and similar content as the current development data) and you need to submit the attack results together with your code. 

**Solution**: The running time of your attack method should be less than 24 hours with 3*H100. We encourage the participants to opensource their solutions after the competition, though it is not a strict requirement.

**Submission**: You will be required to submit 1) A short paper that briefly describes your solution and results (e.g., changes on model utility and attack success rate). The template is available [here](https://github.com/QinbinLi/LLMPC-Red/blob/main/LLMPC-Submission-Template.zip). The main paper is limited to **four content pages**. Additional pages containing references and appendices are allowed; 2) Your predicted jsonl file where each line contains the predicted PII (e.g., {"NAME-1": "xxx"}) of the corresponding line in the test data. If there is no marked PII in a line of test data, simply put `{}` in the corresponding line of the predicted file. You can refer to the structure of [PII](https://github.com/QinbinLi/LLMPC-Red/blob/main/data/LLM-PC-development-pii.jsonl) file of the development data; 3) Your source code and model (if any). The source code should be runnable by `python main.py` and outputs the above jsonl file. Please email your paper, predicted file, and code to <llmpc2024.info@gmail.com> by Nov 1st AOE.



## Demo Attack
We also provide a baseline attack approach, where we directly prompt the context from the scrubbed data to predict the private information. You can run it by the following instructions
```
git clone https://github.com/QinbinLi/LLM-PBE.git
cd LLM-PBE
conda create -n llm-pbe python=3.10 -y
conda activate llm-pbe
pip install -r requirements.txt
python -m attacks.DataExtraction.llm_pc_attack_baseline --model LLM-PBE/Llama3.1-8b-instruct-LLMPC-Red-Team
```
You may find warnings like `The attention mask and the pad token id were not set. xxx`, which is normal. You can find results like `ASR (Attack Success Rate): 2.46% (475/19337)` in the output. Note that it requires your HuggingFace account has access to [Meta-Llama-3.1-8B-Instruct](https://huggingface.co/meta-llama/Meta-Llama-3.1-8B-Instruct). 

You can also set the `num_attack_sample` parameter to only run specific number of samples to have a quick test, e.g.,
```
python -m attacks.DataExtraction.llm_pc_attack_baseline --model LLM-PBE/Llama3.1-8b-instruct-LLMPC-Red-Team --num_attack_sample 1000
```

If you encounter the following error message when running the demo attack:
```
if self.pad_token_id is not None and self.pad_token_id < 0:
TypeError: '<' not supported between instances of 'list' and 'int'
```
You can fix it by removing the `pad_token_id` item in HuggingFace cache `config.json` (e.g., the path may be like `~/.cache/huggingface/hub/models--LLM-PBE--Llama3.1-8b-instruct-LLMPC-Red-Team/snapshots/xxx/config.json`) and run again.

## Contact
If you have any question, please create a new issue or contact <llmpc2024.info@gmail.com>.

## Citation
The attack demo is based on LLM-PBE. If you find it useful, please cite our paper.

```
@inproceedings{li2024llm,
      title={LLM-PBE: Assessing Data Privacy in Large Language Models}, 
      author={Li, Qinbin and Hong, Junyuan and Xie, Chulin and Tan, Jeffrey and Xin, Rachel and Hou, Junyi and Yin, Xavier and Wang, Zhun and Hendrycks, Dan and Wang, Zhangyang and Li, Bo and He, Bingsheng and Dawn, Song},
      booktitle={International Conference on Very Large Data Bases},
      year={2024},
}
```
