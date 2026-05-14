# Use raw PyTorch instead of DeepXDE for PINN implementation

We implement the PINN from scratch using PyTorch rather than using the DeepXDE library. DeepXDE encapsulates the training loop, making it hard for the Agent to pause between rounds, inspect intermediate state, and inject configuration changes. Raw PyTorch gives full control over the training loop, which is essential for the round-based Training Round interaction model where the Agent needs to read Training History and apply Tuning Suggestions after each round.

Status: accepted

Considered Options:
- DeepXDE: higher-level API, less code, but training loop is a black box
- Raw PyTorch: more code to write, but complete control over training loop instrumentation
- DeepXDE with custom callbacks: attempted middle ground, but callback mechanism is too limited for our needs
