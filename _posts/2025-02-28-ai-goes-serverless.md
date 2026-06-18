---
layout: post
title: "AI Goes Serverless: Are Systems Ready?"
date: 2025-02-28 00:00:00 +0000
description: Serverless AI promises pay-per-token pricing and zero infrastructure management — but large AI models expose four deep challenges that traditional serverless systems were never designed to handle.
tags: [serverless, LLM, systems, cloud computing]
categories: [research]
featured: true
related_posts: true
toc:
  sidebar: left
---

> Originally published on [ACM SIGARCH Computer Architecture Today](https://www.sigarch.org/ai-goes-serverless-are-systems-ready/) and [SIGOPS Blog](https://www.sigops.org/2025/ai-goes-serverless-are-systems-ready/), Feb 28, 2025. Co-authored with Luo Mai and Dmitrii Ustiugov.

Artificial Intelligence (AI) is revolutionizing the world, powering productivity tools, healthcare, and education innovations through large-scale models like ChatGPT, DeepSeek, Gemini, and Claude. Most of these models, managed by tech giants such as OpenAI, Google, and Anthropic, require users to send data to centralized services. However, growing concerns over data security and privacy, along with the need for specialized applications, are driving developers and companies to deploy private AI models fine-tuned (e.g., based on DeepSeek) or pre-trained on proprietary datasets. A recent Forbes article featuring VMware notes that this trend underscores a shift toward custom models tailored to specific needs.

Serving custom AI models, however, introduces significant challenges. The high costs of provisioning specialized hardware (e.g., GPUs, TPUs, or NPUs) and the complexities of infrastructure maintenance create barriers. For individual users and small companies, inference requests are often unpredictable and spiky. Reserving resources to meet peak demand frequently leads to substantial waste — even if such demand can be accurately estimated. Additionally, deploying AI models efficiently on specialized hardware is far from straightforward. It requires choosing the most cost-effective hardware (e.g., CPU vs. GPU vs. NPU, A100 vs. H100), configuring the model appropriately (e.g., parallelism methods, batch size), and selecting the optimal inference engine (e.g., vLLM, TensorRT LLM, llama.cpp). These factors are deeply interdependent. For instance, the best inference engine depends on the model and hardware. At the same time, the optimal batch size relies on the degree of parallelism, influenced by both inference traffic and hardware choice. These intricacies render custom model serving inefficient and hinder widespread AI adoption.

## The Promise of Serverless AI

Serverless AI makes custom model serving both efficient and feasible by reducing costs and simplifying deployment efforts. In a serverless AI setup, developers upload model checkpoints to repositories like HuggingFace without worrying about accelerator types, inference engines, or replica counts. The cloud provider manages scheduling and infrastructure configuration to ensure efficient execution. This paradigm enables developers to pay only for actual inference usage (e.g., cost per token) rather than reserving resources based on uptime. This token-based, pay-as-you-go billing model incentivizes cloud providers to optimize resource utilization while meeting Service Level Objectives (SLOs). As a result, models automatically scale between zero to many instances to accommodate the current inference request traffic. Model checkpoints are loaded into accelerators (e.g., GPUs, TPUs) only when requests are received, allowing unused accelerators to serve other models. This approach simplifies deployment for developers while maximizing resource efficiency for cloud providers, creating a mutually beneficial ecosystem.

Take HuggingFace as an example. As one of the largest AI model repositories, it offers both non-serverless and serverless deployment options:

- **Inference Endpoints** are dedicated instances with extensive configuration options. Users must select from a wide range of hardware configurations, inference engines, and replica counts. Navigating thousands of potential combinations can be daunting, often leading to suboptimal performance or excessive costs for non-experts.
- **The Inference API** is a serverless solution in which users simply send inference requests, specifying the model name (assuming the checkpoint has been uploaded) and input data. This abstracts all infrastructure complexities, eliminating concerns about hardware, inference engines, or scaling strategies.

This example highlights how Serverless AI simplifies custom model deployment by removing infrastructure management burdens. Developers can focus on building innovative models and applications while cloud providers handle resource scheduling and optimization, including auto-scaling and efficient resource utilization.

## Challenges Multiplied: Serverless Computing Meets AI

Despite its benefits, Serverless AI magnifies existing inefficiencies in serverless computing, especially when deploying large AI models on specialized hardware. These challenges present opportunities for systems researchers, as current AI infrastructures are seldom designed with serverless paradigms in mind, and traditional serverless systems are not tailored for state-of-the-art (SOTA) AI workloads. Consequently, we identify four primary challenges in Serverless AI: cold-start latency, state management, communication patterns, and scheduling.

### 1. Cold-Start Latency

A major challenge in Serverless AI is cold-start latency — the time required to launch a new inference instance. While traditional serverless computing has seen numerous efforts to reduce cold-start times, large AI models introduce new complexities. Two factors contribute significantly:

- Loading large model checkpoints (up to terabytes in size) from remote storage into accelerators.
- Initializing accelerator execution contexts, such as setting up CUDA graphs.

Although recent advancements like [ServerlessLLM](https://arxiv.org/abs/2401.14351) have improved loading speeds to approach hardware bandwidth limits, challenges remain, such as optimizing checkpoint placement and caching. Furthermore, initializing specialized hardware environments is more complex than setting up traditional CPU environments, making cold starts a critical bottleneck for Serverless AI.

### 2. State Management

AI inference is inherently stateful for performance optimizations, a stark contrast to the stateless nature of traditional serverless functions. For instance, Large Language Model (LLM)-based chat applications rely on previous key-value (KV) caches to accelerate inference. While making the process stateless by recomputing the KV cache for each request is possible, it is highly inefficient and impractical for production environments. Similarly, Retrieval-Augmented Generation (RAG) inference requires access to large amounts of embedding data during inference. Fetching embeddings from remote vector databases introduces significant latency. Such dependencies on maintaining state complicate AI workloads' integration with conventional serverless architectures.

### 3. Communication Patterns

AI workloads often require inter-function communication, unlike traditional serverless designs. For instance, workflows chaining multiple models — such as Chain-of-Thought inference or RAG prompting — demand fast data transfers between functions. Optimizations like using NVLink or shared memory enable efficient intra-node communication but violate serverless principles discouraging tight coupling. These unique communication demands complicate deployment and reveal gaps in traditional serverless architectures.

### 4. Scheduling

Deploying AI models adds complexity to scheduling decisions, such as selecting accelerator types, choosing inference engines, and configuring parameters like batch size and parallelism. However, while Serverless AI abstracts these complexities away from the user, the responsibility to manage them effectively falls on the cloud provider. Misconfigurations can lead to significant resource underutilization or performance bottlenecks. Additionally, AI inference's stateful and non-deterministic nature complicates traditional scheduling tasks like load balancing. These challenges highlight the need for innovative scheduling algorithms tailored to Serverless AI.

## Open Source Tools to Enable Research in Serverless AI Systems

The open-source ecosystem provides essential building blocks for exploring the entire stack of AI systems. DeepSeek has released all its models including V3 and R1. AMD has released ROCm, the low-level programming framework for AMD GPUs. PyTorch has become a mature deep learning framework with comprehensive GPU optimizations, while vLLM delivers specialized inference acceleration for LLMs through innovations such as continuous batching and PagedAttention. All of these projects are accessible on GitHub, enabling direct collaboration with their maintainers and developers. Additionally, Microsoft Azure has released their production LLM inference traces, offering researchers an opportunity to evaluate systems under realistic workloads.

[ServerlessLLM](https://github.com/ServerlessLLM/ServerlessLLM), integrated with the aforementioned open-source ecosystem, provides a high-performance research platform designed for ease of deployment. Its modular architecture allows researchers to dive into various system components (e.g., load balancing, scheduling, or storage subsystems), explore co-design opportunities, and prototype novel ideas. Built with a native Python interface and RESTful APIs, it is straightforward to set up, customize, and debug on both research GPU clusters and personal computers — making it more accessible than alternatives such as KServe and Ray Serve. Additionally, its fast checkpoint loading, live migration, and locality-aware scheduling deliver state-of-the-art serverless inference performance as a robust baseline for further experimentation.

With open-source systems such as ServerlessLLM and these resources readily available, researchers can now focus on tackling the core challenges in Serverless AI systems outlined above, from optimizing cold starts to designing novel scheduling algorithms for the next generation of AI infrastructure.
