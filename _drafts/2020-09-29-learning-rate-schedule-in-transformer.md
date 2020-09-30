---
layout: post
title: Learning rate schedules in Transformer
description: Here are some of the learning rates used in iconic transformer models
tags:
- name: transformer
- name: learning_rate_schedule
- name: optimizer
category: ml
permalink: /blog/:title.html
---

When training a large model, the
There is a general consensus that you start with a few thousand warmup steps that afterwards reaches to the maximum learning rate, and then slowly decay the learning rate down. But there are subtle differences in the way that the learning rate decays.

![_config.yml]({{ site.baseurl }}/img/posts/learning_rate_schedule_in_transformer/overview.png)
<center>Figure 1. Overview of learning rate schedules</center>
This graph is the overview of learning rate schedules used in training some of the well-known large transformer models. *The learning rate schedules are vertically scaled so that the maximum learning rate schedule is roughly around `1e-3`*.

### [Attention is all you need](https://arxiv.org/pdf/1706.03762.pdf)
![_config.yml]({{ site.baseurl }}/img/posts/learning_rate_schedule_in_transformer/transformer.png)
<center>Figure 2. OG transformer - rsqrt decay with warmup</center>

*The learning rate is specified in section 5.3 of the paper [Attention is All You Need](https://arxiv.org/pdf/1706.03762)*.

The learning rate schedule used in OG transformer paper is **rsqrt_decay** with linear warmup steps. It is interesting that it does not explicitly specify maximum learning rate, but scale the learning rate with `rsqrt(embedding_dim)` of the model.

#### Formula
$$
lr = d_{model} ^ {-0.5} * min(step ^ {-0.5}, \ step * warmup\_steps ^ {-1.5})
$$

#### Pseudocode
```python
def transformer_lr(step, embedding_dim, warmup_steps):
    lr = min(rsqrt(step), step * (warmup_steps**(-1.5)))
    return lr * rsqrt(embedding_dim)
```

### [BERT](https://arxiv.org/abs/1810.04805)
![_config.yml]({{ site.baseurl }}/img/posts/learning_rate_schedule_in_transformer/slanted_triangle.png)
<center>Figure 3. BERT - linear decay with warmup</center>
*The learning rate is specified in Appendix A.2 of the paper [BERT  Pre-training of Deep Bidirectional Transformers for
Language Understanding](https://arxiv.org/pdf/1810.04805).*

There are many different ways of calling this schedule (e.g. linear decay with warmup, slanted triangle .. ) and is perhaps the simplest form of the learning rate decay.

*Note: It is important to specify the total steps in the function because the learning rate at the last step falls to 0.*

*Note 2: Or you can simply modify the schedule so that it has the `min_lr`*

#### Formula
$$
lr =
\begin{cases}
step * max\_lr / warmup\_steps  & \text{if $step \le warmup\_steps$} \\
max\_lr - max\_lr * (step - warmup\_steps)\ /\ total\_steps & \text{else}
\end{cases}
$$
#### Pseudocode
```python
def slanted_triangle(step, lr, warmup_steps, total_steps):
    if step <= warmup_steps:
        return step * lr / warmup_steps
    else:
        return lr - lr * (step - warmup_steps) / total_steps
```
### GPT-3
`cosine decay`

### Mesh tensorflow
Similar to transformer - agnostic of embedding dim, no warmup steps

`rsqrt (noam's favorite)`



