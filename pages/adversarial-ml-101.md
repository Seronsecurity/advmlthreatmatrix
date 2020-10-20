# Adversarial Machine Learning 101 
Informally, Adversarial ML is “subverting machine learning systems for fun and profit”. The methods underpinning the production machine learning systems are systematically vulnerable to a new class of vulnerabilities across the machine learning supply chain collectively known as Adversarial Machine Learning. Adversaries can exploit these vulnerabilities to manipulate AI systems in order to alter their behavior to serve a malicious end goal.

Consider a typical ML pipeline shown in the left that is gated behind an API, wherein the only way to use the model is to send a query and observe an response.  In this example, we assume a blackbox setting: the attacker does NOT have direct access to the training data, no knowledge of the algorithm used and no source code of the model. The attacker only queries the model and observes the response. 


**End-to-End vs Hand-Crafted features:** 

In an end-to-end system, the data is collected in the state in which it will be introduced to the algorithm during training. Images are the best example of this. An image already contains all the necessary information about itself. Consider a 28x28 grayscale image, this image has 784 pixels where each pixel is a value between 0-255. Alternatively, hand-crafted features represent custom transformations on data before being introduced to an algorithm at train time. From an attack perspective, end-to-end systems 

**Train time vs Inference time:** 

Training refers to the process by which data is modeled. This process includes collecting and processing data, training a model, validating the model works, and then finally deploying the model. An attack that happens at "train time" is an attack that happens while the model is learning prior its deployment. After a model is deployed, consumers of the model can submit queries and receive outputs (inferences). An attack that happens at "inference time" is an attack where the learned state of the model does not change and the model is just providing outputs. In practice, a model could be re-trained after every new query providing an attacker with some interesting scenarios by which they could use an inference endpoint to perform a "train-time" attack. In any case, the delination is useful to describe how an attacker could be interacting with a target model.

**Online vs Offline:**

An "online" model is typically the production model. An "offline" model represents a local copy of a model.  

With this in mind, we can jump into the attacks on ML systems 

![Adversarial ML 101](/images/AdvML101.PNG)


# Machine Learning Attacks
Attacks on machine learning systems can be categorized as follows.

| Attack 		        | Overview	| Type |
| :---			        | :---      | :---:|
| Model Evasion         | Attacker modifies a query in order to get a desired outcome. These attacks are performed by iteratively querying a model and observing the output. | Inference |
| Functional Extraction | Attacker is able to recover a functionally equivalent model by iteratively querying the model. This allows an attacker to examine the offline copy of the model before further attacking the online model | Inference |
| Model Poisoning 	    | Attacker contaminates the training data of an ML system in order to get a desired outcome at inference time. With influence over training data an attacker can create "backdoors" where an arbitrary input will result in a particular output. The model could be "reprogrammed" to perform a new undesired task. Furhter, access to training data would allow the attacker to create an offline model and create a Model Evasion. Access to training data could also result in the compromise of private data  | Train |
| Model Inversion 	    | Attacker recovers the features used to train the model. A successful attack would result in an attacker being able to launch a Membership inference attack. This attack could result in compromise of private data | Inference | 
| Traditional Attacks   | Attacker uses well established TTPs to attain their goal. | Both |


# Attack Scenarios
## Attack Scenario #1: API Deployment
Consider the most common deployment scenario where a model is deployed as an API endpoint. In this blackbox setting an attacker can only query the model and observe the response. The attacker controls the input to the model, but the attacker does not know how it is processed. 

![Adversarial ML 101](/images/AdvML101_Inference.PNG)

| Examples | |
|:---      | :--- |
|**Model Evasion**| An attacker wants to get a piece of malware classified as benign. The attacker iteratively queries the model and either manually or algorithmically changes the malware between each submission until the model the desired classification has been obtained. If the model is an end-to-end system alterations to the malware are relatively simple. However, if the model uses hand-crafted features, the attacker might need to use their domain knowledge to figure out what aspects of the malware sample the model thinks are most malicious. In reality, the attacker will use their domain knowledge in both scenarios, but if the attacker has no domain knowledge. |
|**Model Poisoning**| Model poisoning is not possible. The model is not being trained and is only providing outputs to the attacker.|
|**Model Inversion**| An attacker wants to recover PII from a language model.| 
|**Functional Extraction**| An attacker wants to create a copy-cat model, or recreate a models private functionality.|


---- 
## Attack Scenario #2: Pipeline Compromise
Consider that an attacker has control over training data and can query the deployed model.


![Adversarial ML 101](/images/AdvML101_Traintime.PNG)

| Examples | |
|:---      | :--- |
|**Model Evasion**| Is a result of successful poisoning. |  
|**Model Poisoning**| With access to the training and deployment pipeline, an attacker can...|
|**Model Inversion**| The attacker already has access to training data.|
|**Functional Extraction**| A copy-cat model trained on the same data |



## Attack Scenario #3: Client Deployment
Consider that a model exists on a client. An attacker might have access to model code through reversing the service on the client. 

![Adversarial ML 101](/images/AdvML101_Client.PNG)

| Examples | |
|:---      | :--- |
|**Model Evasion**| Is a result of successful poisoning. |  
|**Model Poisoning**| With access to the training and deployment pipeline, an attacker can...|
|**Model Inversion**| The attacker already has access to training data.|
|**Functional Extraction**| A copy-cat model trained on the same data |

# Important Notes
1.  Adversarial ML is an active area of research.
2.  Machine learning is vulnerable wherever it is deployed.
4.  These attack are possible for all datatypes, images, audio, timeseries, or tabular data.
