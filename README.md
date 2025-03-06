# Telecom-Alarms-Root-Cause-Detection
This repository addresses the problem of representing, generating, and detecting alarms in Telecom systems that are root causes to failures. We split the workflow  into 3 phases:
A.	Alarm Representation Phase:
Since BERT was firstly proposed in 2018 many variants were proposed with different parameter number, architecture, and training methods including Base Bert (110M params, 12 layers, 768 embedding size), Distil Bert (66M, 6 layers, 768 embedding size) and Tiny Bert (4.4M, 2 layers, 128 embedding size). We chose Distil BERT as a pre-trained model having adequate language understanding along with fewer layers to fine-tune, and used it to build our customized sentence transformer which can embed full titles into vectors. 

B.	Root Cause Generation Phase:
We propose to predict root alarms through alarm generation using decoder (custom small GPT) rather than classification using encoders. To train out decoder we need alarms to be segmented and embedded using our fine-tuned BERT. Note that while using GPT no need for root alarms to be present in the processed segments. As we explained in both the methodology and Figure 5, GPT must firstly be pre-trained on next-alarm prediction self-supervised task, then on summarization task to learn potential root candidates. After that we fine-tune the model on qualitatively-labeled scenario roots dataset to improve our model root prediction performance. The output of this stage is a root alarm list sorted from most to least probable roots, for each input scenario.

C.	Model Optimization Phase:
In this phase, the resulted root cause generation model is treated as an initial policy that must be optimized though expert feedback. Knowing that we only managed to train our model in the last phase on a small sample (~20). The optimization is conducted using PPO algorithm after training a so-called “reward model” which plays the role of an evaluator that itself is trained by seeing experts sorting the root list for the model (policy). The trained reward model is then used to reward the policy in a reinforcement learning loop resulting a better policy after each iteration. Lastly, the updated policy can be deployed to our FMS after being tested and accomplished satisfactory results.
