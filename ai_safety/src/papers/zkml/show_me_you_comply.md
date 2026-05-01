# Show Me You Comply... Without Showing Me Anything (Oct 2025)

## Context 

- Regulations are incoming like EU AI Act
- auditors need transparency into AI models
- frontier labs want to protect models weights and training data

__How can ZKP can be used to systematically audit the MLOps lifecycle?__

  
## Related work 

- inference: ZEN, vCNN, zkCNN
- training: ZKAudit and zkDL
- online metrics verification 
- trustworthy AI: ZKAudit


## Contributions

- ZKMLOps Framework
  - architecture integrating ZKP-based verification into MLOps lifecycle
- Real-world use case
  - applied to regulatory compliance in financial risk auditing
- Empirical Evaluation 
  - benchmark of ZKP procols on ML models of increasing complexity
  - SNARK, STARK, ezkl, GKR
  - FNN, small CNN, MNIST CNN, LeNet5
  - analyze proving time, verification time and proof size
  
### ZKMLOps

- MLOps verification phase
  - data & preprocessing, training & offline metrics, inference, online metrics
- pick appropriate ml model category
  - CNNs, LLMs, decision trees, etc
- picking the most appropriate ZKP protocol
  - non-interactivity, transparent setup, standard representations, succinctness and post-quantum security
