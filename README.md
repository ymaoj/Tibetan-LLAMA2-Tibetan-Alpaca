# Tibetan-LLAMA2和Tibetan-Alpaca
Tibetan-LLAMA2和Tibetan-Alpaca模型是本文在藏文自然语言处理领域的重要研究成果。这两个模型结合了最新的增量预训练和指令微调技术，在较大规模藏文语料库上训练而成。它们继承了大型预训练语言模型的优点，例如强大的语言理解和生成能力，以及能够处理各种复杂的语言任务。
本文Tibetan-LLAMA2模型的特点在于对藏文特有表达方式和文化背景的深入理解，该模型通过对较大规模数据进行增量预训练，掌握了藏文的语言结构和表达习惯，在藏文文本续写任务中表现出色。
本文Tibetan-Alpaca模型则更加注重藏文专业领域的知识。该模型在训练过程中，吸收了多个下游任务数据，从而在处理涉及专业知识的任务时表现更佳。
## 藏文词表扩充
LLAMA2中有十五个藏文字构建，分别是“་”、“ས”、“ོ”、“ག”、“ར”、“བ”、“ད”、“ང”、“ི”、“ུ”、“ན”、“མ”、“ེ”、“ལ”、“ྱ”。说明LLAMA2训练时使用了少量的藏文语料，但其理解和生成藏文文本的能力是有限的。为了使LLAMA2具有更好的藏文理解和生成能力，本文尝试使用藏文语料对LLAMA2模型进行增量预训练。为了增强分词器对藏文文本的支持，首先在藏文纯文本数据39.6G上通过SentencePiece技术训练藏文分词器，随后将藏文分词器合并到原始的LLAMA2分词器中。因此，得到了一个合并的分词器，本文中称之为Tibetan-LLAMA2分词器，该分词器的词汇量为54949。其中32000是原始LLAMA2的词汇量，54949是扩充后的Tibetan-LLAMA2分词器的新词汇量，扩充了22949个藏文标记。

|     分词方法          	|     长度    	|     分词结果                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      	|
|-----------------------	|-------------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
|     音节切分          	|     16      	|     'ཁྱོད','ཀྱི','གདོང','ནི','པད','མ','བཞིན','།','།','མིག','དག','ཨུཏྤལ','སྔོན','པོ','བཞིན','།'                                                                                                                                                                                                                                                                                                                                                                                                                                                  	|
|     LLAMA2            	|     81      	|     '▁','<0xE0>','<0xBD>','<0x81>','ྱ','ོ','ད','་','<0xE0>','<0xBD>','<0x80>','ྱ','ི','་','ག','ད','ོ','ང','་','ན','ི','་','<0xE0>','<0xBD>','<0x94>','ད','་','མ','་','བ','<0xE0>','<0xBD>','<0x9E>','ི','ན','<0xE0>','<0xBC>','<0x8D>','<0xE0>','<0xBC>','<0x8D>','མ','ི','ག','་','ད','ག','་','<0xE0>','<0xBD>','<0xA8>','ུ','<0xE0>','<0xBD>','<0x8F>','<0xE0>','<0xBE>','<0xA4>','ལ','་','ས','<0xE0>','<0xBE>','<0x94>','ོ','ན','་','<0xE0>','<0xBD>','<0x94>','ོ','་','བ','<0xE0>','<0xBD>','<0x9E>','ི','ན','<0xE0>','<0xBC>','<0x8D>'    	|
|     Tibetan-LLAMA2    	|     10      	|     '▁ཁྱོད་ཀྱི་','གདོང་','ནི་','པད་མ་','བཞིན།།','མིག་','དག་','ཨུཏྤལ་','སྔོན་པོ་','བཞིན།'                                                                                                                                                                                                                                                                                                                                                                                                                                                        	|
## 参数设置

|     设置                   	|     Tibetan-LLAMA2-7B/13B    	|     Tibetan-Alpaca-7B/13B    	|
|----------------------------	|-----------------------------------	|-----------------------------------	|
|     Training data          	|     39.6GB                        	|     715K                          	|
|     Epochs                 	|     1                             	|     1                             	|
|     Batch size             	|     1                             	|     4                             	|
|     Dropout Rate           	|     -                             	|     0.05                          	|
|     Peak learning rete     	|     2e-4                          	|     1e-4                          	|
|     Max sequence length    	|     1024                          	|     1024                          	|
|     LoRA rank              	|     64                            	|     64                            	|
|     LoRA alpha             	|     128                           	|     128                           	|
|     LoRA weights           	|     QKVO,MLP                      	|     QKVO,MLP                      	|
|     Training Precision     	|     bf16                          	|     bf16                          	|
|     训练时长               	|     348h/673h                     	|     362h/535h                     	|

## 实验数据介绍
对于Tibetan-LLAMA2模型，本文利用39.6GB的藏文纯文本进行预训练，增强了模型对基本概念的理解。
在获得Tibetan-LLAMA2模型后对其进行指令微调。通过将LoRA模块添加到基座模型的所有线性层，继续使用LoRA进行有效的微调。本文加入包括语言理解、生成和信息提取在内的多种指令提示数据集上训练模型。使用了大约715128条指令数据（4.37GB），包括开放问答、摘要生成、新闻生成、释义生成、时态生成以及文本分类等数据，具体如表3所示。用于指令微调的Tibetan-Alpaca模型的详细超参数如表2所示。所有Alpaca模型都是基于各自的LLAMA2模型进行训练的。例如，Tibetan-Alpaca-13B是在Tibetan-LLAMA2-13B上训练的。
|     类型        	|     数量      	|
|-----------------	|---------------	|
|     释义生成    	|     58544     	|
|     时态生成    	|     2484      	|
|     新闻生成    	|     241292    	|
|     开放问答    	|     170449    	|
|     摘要生成    	|     234166    	|
|     文本分类    	|     8193      	|
## 实验结果分析
为了评估Tibetan-LLAMA2和Tibetan-Alpaca模型的性能，本文进行了一系列实验。由于还没有公开的藏文大模型性能评估工具或数据集，因此本文使用了两个自建数据集来评估模型的性能。目前还未发布藏文大模型相关的模型和性能评估后的数据，因此本文只比较了本文所训练的几个模型的实验结果。通过本文实验结果表明，Tibetan-LLAMA2和Tibetan-Alpaca在客观效果评测和文本生成评测的各项指标上均取得了较好的提升，证明了它们在藏语自然语言处理中的优越性。由于大语言模型的评估是一项具有挑战性的工作，缺乏标准化的藏文大模型的评估基准，使评估工作变得复杂，尽管本文采用的评估方法有一定的参考性，但也无法准确衡量模型在不同领域的性能。
### 客观效果评测
本文在构建好的机器阅读理解中多项选择数据集中随机挑选了两千条数据进行0-shot和few-shot（5-shot）实验，其验证集包含1600条数据，测试集包含400条数据，实验结果如表4所示。本实验在测试集上比较了四种不同的模型（Tibetan-LLAMA2-7B、Tibetan-LLAMA2-13B、Tibetan-Alpaca-7B、Tibetan-Alpaca-13B），性能是通过准确率（Accuracy）这一指标来衡量的。零样本评估直接使用预训练模型进行测试，不依赖任何额外标注数据；少样本评估则在预训练模型的基础上，使用少量标注数据进行微调，然后进行测试。
|     Model                 	|     0-shot(%)    	|     few-shot(%)    	|
|---------------------------	|------------------	|--------------------	|
|     Tibetan-LLAMA2-7B     	|     21.59        	|     24.81          	|
|     Tibetan-LLAMA2-13B    	|     22.08        	|     27.05          	|
|     Tibetan-Alpaca-7B     	|     25.31        	|     26.80          	|
|     Tibetan-Alpaca-13B    	|     26.98        	|     28.54          	|

### 文本生成评测
本文建立了一个相对可靠的评估框架，有效地衡量了本文的Tibetan-Alpaca模型在一系列自然语言理解和生成任务上的表现。本文的评估集旨在全面评估Tibetan-Alpaca模型在广泛的藏语自然语言理解和生成任务。该评估集包括12000个样本，涵盖六个不同的任务，包括摘要生成、新闻生成、开放问答、时态生成、释义生成和文本分类等特定任务。通过原始答案与生成的答案之间计算Rouge-L分数来评价模型性能。这种方法确保反映模型在各种任务中的能力，提供了对其性能的平衡和鲁棒的度量。由于Tibetan-LLAMA2模型只具备文本续写能力，无法在该模型上进行各下游任务的文本生成评测，因此本文只在Tibetan-Alpaca模型上进行比较。
|     Model       	|     Tibetan-Alpaca-7B    	|     Tibetan-Alpaca-13B    	|
|-----------------	|--------------------------	|---------------------------	|
|     摘要生成    	|     31.70                	|     43.84                 	|
|     新闻生成    	|     1.58                 	|     3.77                  	|
|     开放问答    	|     5.41                 	|     7.89                  	|
|     时态生成    	|     83.50                	|     85.49                 	|
|     释义生成    	|     9.94                 	|     12.76                 	|
|     文本分类    	|     76.63                	|     78.94                 	|
|     Avg         	|     32.79                	|     38.78                 	|

## 致谢
感谢LLAMA2研究开发人员及相关项目，感谢Chinese-LLaMA-Alpaca-2项目以及相关人员。

## 免责声明
本项目基于由Meta发布的Llama-2模型进行开发，使用过程中请严格遵守Llama-2的开源许可协议。如果涉及使用第三方代码，请务必遵从相关的开源许可协议。模型生成的内容可能会因为计算方法、随机因素以及量化精度损失等影响其准确性，因此，本项目不对模型输出的准确性提供任何保证，也不会对任何因使用相关资源和输出结果产生的损失承担责任。如果将本项目的相关模型用于商业用途，开发者应遵守当地的法律法规，确保模型输出内容的合规性，本项目不对任何由此衍生的产品或服务承担责任。
### 局限性声明
虽然本项目中的模型具备一定的中文理解和生成能力，但也存在局限性，包括但不限于：

可能会产生不可预测的有害内容以及不符合人类偏好和价值观的内容
由于算力和数据问题，相关模型的训练并不充分，中文理解能力有待进一步提升
暂时没有在线可互动的demo（注：用户仍然可以自行在本地部署和体验）



