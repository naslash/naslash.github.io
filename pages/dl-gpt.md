---
layout: page
title: "Deep Learning: Mini GPT-Style Decoder-Only Transformer"
permalink: /dlgpt
---

In this project, I trained a small GPT-style decoder-only Transformer language model with PyTorch (using CUDA) to generate short children’s stories.
<br><br>

## About the data
I used the TinyStories dataset (Eldan & Li, 2023), a synthetic dataset of children’s short stories written at a child's reading level. This dataset was chosen because to its simpler vocabulary, grammar, and cause-effect relationships make it well suited for training a small language model.

Due to hardware constraints, I trained the model using only the first 50,000 stories from the dataset. While this limited the model's performance, it was sufficient for the model to generate short stories with reasonable coherence given the limited training data and small model size.
<br><br>

## Model description
A small GPT-style decoder-only Transformer language model is trained from scratch to generate children's short stories with as much coherence as possible given the available hardware. The model uses word-level tokenization for next-token prediction, where each token represents a complete word. I implemented a custom PyTorch `Dataset` class that tokenizes each story before supplying the resulting tokens to the model through PyTorch's `DataLoader`.

The model architecture is based on a class lecture. Several core modules were adapted directly from the lecture, including the decoder block, causal self-attention, feed-forward network, and positional encoding.

For text generation, there are two main parameters that further control token selection. The *temperature* parameter controls the randomness of token selection. A value of 0.0 performs *greedy* decoding, in which the highest-probability token is always selected, while higher values increase the randomness of the generated text. The *top-k* parameter restricts sampling to the *k* most probable tokens at each prediction step.
<br><br>

## Training and validating
I used the `Adam` optimizer with PyTorch's `CrossEntropyLoss` as the loss function. I also tracked the training and validation *loss* and *perplexity* for each epoch to analyze how different hyperparameter choices influenced model performance. The *embedding layer* was trained from scratch rather than initialized with pre-trained embeddings.

I first performed a hyperparameter sweep to identify the best-performing hyperparameter configuration, which included *d_model*, *num_heads*, *num_layers*, *learning rate*, *dropout*, and *batch size*. Between successive sweeps, I plotted and compared many different configurations based on the results to better understand how each hyperparameter affected model performance and to guide the next round of hyperparameter tuning. I have omitted many intermediate tables and figures to keep this presentation concise. The following results are from the best-performing configuration.

Model Parameters: 6,026,253  
Validation Loss: 2.6977310702204704

Best Model Results:

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe {
        width: 100%
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe" style="font-size:14px;">
  <thead>
    <tr style="text-align: right;">
      <th>Best Epoch</th>
      <th>Sequence Length</th>
      <th>Batch Size</th>
      <th>Dropout</th>
      <th>Learning Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>16</td>
      <td>128</td>
      <td>64</td>
      <td>0.1</td>
      <td>0.01</td>
    </tr>
  </tbody>
</table>
</div>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe {
        width: 100%
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe" style="font-size:14px;">
  <thead>
    <tr style="text-align: right;">
      <th>Epoch</th>
      <th>Training Loss</th>
      <th>Training Perplexity</th>
      <th>Validation Loss</th>
      <th>Validation Perplexity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>4.140937</td>
      <td>62.861665</td>
      <td>3.591822</td>
      <td>36.300151</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3.393987</td>
      <td>29.784453</td>
      <td>3.147891</td>
      <td>23.286912</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.074499</td>
      <td>21.639038</td>
      <td>2.975334</td>
      <td>19.596170</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2.915170</td>
      <td>18.451955</td>
      <td>2.881293</td>
      <td>17.837318</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2.816623</td>
      <td>16.720287</td>
      <td>2.825630</td>
      <td>16.871564</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2.745871</td>
      <td>15.578177</td>
      <td>2.800440</td>
      <td>16.451877</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2.693304</td>
      <td>14.780433</td>
      <td>2.768121</td>
      <td>15.928671</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2.649883</td>
      <td>14.152377</td>
      <td>2.742993</td>
      <td>15.533411</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2.615118</td>
      <td>13.668822</td>
      <td>2.738034</td>
      <td>15.456566</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2.585040</td>
      <td>13.263814</td>
      <td>2.722811</td>
      <td>15.223054</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2.558785</td>
      <td>12.920114</td>
      <td>2.717518</td>
      <td>15.142685</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2.537040</td>
      <td>12.642196</td>
      <td>2.708495</td>
      <td>15.006667</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2.518340</td>
      <td>12.407982</td>
      <td>2.717752</td>
      <td>15.146241</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2.499739</td>
      <td>12.179321</td>
      <td>2.705827</td>
      <td>14.966696</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2.484050</td>
      <td>11.989722</td>
      <td>2.706571</td>
      <td>14.977828</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2.469094</td>
      <td>11.811741</td>
      <td>2.711926</td>
      <td>15.058245</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2.456041</td>
      <td>11.658569</td>
      <td>2.697731</td>
      <td>14.846009</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2.443953</td>
      <td>11.518485</td>
      <td>2.706023</td>
      <td>14.969617</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2.432776</td>
      <td>11.390453</td>
      <td>2.698823</td>
      <td>14.862225</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2.422492</td>
      <td>11.273924</td>
      <td>2.699930</td>
      <td>14.878683</td>
    </tr>
  </tbody>
</table>
</div>

Next, I trained the final model using the best-performing hyperparameter configuration for additional epochs to ensure that training had converged. While this approach is not guaranteed to produce the best possible model, it was the most practical option given the available hardware resources. The following results are from the final model.

Model Parameters: 6,026,253  
Validation Loss: 2.6653746888041496

Best Model Results:

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe {
        width: 100%
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe" style="font-size:14px;">
  <thead>
    <tr style="text-align: right;">
      <th>Best Epoch</th>
      <th>Sequence Length</th>
      <th>Max Length</th>
      <th>Batch Size</th>
      <th>Dropout</th>
      <th>Learning Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>90</td>
      <td>128</td>
      <td>128</td>
      <td>64</td>
      <td>0.1</td>
      <td>0.001</td>
    </tr>
  </tbody>
</table>
</div>

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe {
        max-width: 100%;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe" style="font-size:14px;">
  <thead>
    <tr style="text-align: right;">
      <th>Epoch</th>
      <th>Training Loss</th>
      <th>Training Perplexity</th>
      <th>Validation Loss</th>
      <th>Validation Perplexity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>4.820872</td>
      <td>124.073263</td>
      <td>4.110525</td>
      <td>60.978739</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4.008784</td>
      <td>55.079868</td>
      <td>3.822995</td>
      <td>45.740999</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.746399</td>
      <td>42.368260</td>
      <td>3.589773</td>
      <td>36.225850</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3.542504</td>
      <td>34.553346</td>
      <td>3.425340</td>
      <td>30.733078</td>
    </tr>
    <tr>
      <th>5</th>
      <td>3.394364</td>
      <td>29.795703</td>
      <td>3.302847</td>
      <td>27.189944</td>
    </tr>
    <tr>
      <th>6</th>
      <td>3.285380</td>
      <td>26.719126</td>
      <td>3.223708</td>
      <td>25.121100</td>
    </tr>
    <tr>
      <th>7</th>
      <td>3.200782</td>
      <td>24.551721</td>
      <td>3.156444</td>
      <td>23.486939</td>
    </tr>
    <tr>
      <th>8</th>
      <td>3.131748</td>
      <td>22.913996</td>
      <td>3.101847</td>
      <td>22.238997</td>
    </tr>
    <tr>
      <th>9</th>
      <td>3.074139</td>
      <td>21.631243</td>
      <td>3.058885</td>
      <td>21.303798</td>
    </tr>
    <tr>
      <th>10</th>
      <td>3.024254</td>
      <td>20.578650</td>
      <td>3.031214</td>
      <td>20.722364</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2.982276</td>
      <td>19.732678</td>
      <td>2.996810</td>
      <td>20.021569</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2.944840</td>
      <td>19.007627</td>
      <td>2.966397</td>
      <td>19.421810</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2.911318</td>
      <td>18.381003</td>
      <td>2.944779</td>
      <td>19.006456</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2.882057</td>
      <td>17.850959</td>
      <td>2.927718</td>
      <td>18.684944</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2.854992</td>
      <td>17.374297</td>
      <td>2.908818</td>
      <td>18.335113</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2.830702</td>
      <td>16.957367</td>
      <td>2.889127</td>
      <td>17.977609</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2.807327</td>
      <td>16.565582</td>
      <td>2.881154</td>
      <td>17.834847</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2.786393</td>
      <td>16.222394</td>
      <td>2.862182</td>
      <td>17.499673</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2.767007</td>
      <td>15.910947</td>
      <td>2.852881</td>
      <td>17.337658</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2.749957</td>
      <td>15.641953</td>
      <td>2.844630</td>
      <td>17.195198</td>
    </tr>
    <tr>
      <th>21</th>
      <td>2.732816</td>
      <td>15.376130</td>
      <td>2.831354</td>
      <td>16.968415</td>
    </tr>
    <tr>
      <th>22</th>
      <td>2.717373</td>
      <td>15.140500</td>
      <td>2.819619</td>
      <td>16.770468</td>
    </tr>
    <tr>
      <th>23</th>
      <td>2.702260</td>
      <td>14.913403</td>
      <td>2.811020</td>
      <td>16.626862</td>
    </tr>
    <tr>
      <th>24</th>
      <td>2.687733</td>
      <td>14.698324</td>
      <td>2.805063</td>
      <td>16.528115</td>
    </tr>
    <tr>
      <th>25</th>
      <td>2.675002</td>
      <td>14.512373</td>
      <td>2.791970</td>
      <td>16.313118</td>
    </tr>
    <tr>
      <th>26</th>
      <td>2.662630</td>
      <td>14.333933</td>
      <td>2.781700</td>
      <td>16.146447</td>
    </tr>
    <tr>
      <th>27</th>
      <td>2.650736</td>
      <td>14.164454</td>
      <td>2.777661</td>
      <td>16.081369</td>
    </tr>
    <tr>
      <th>28</th>
      <td>2.638898</td>
      <td>13.997768</td>
      <td>2.784217</td>
      <td>16.187135</td>
    </tr>
    <tr>
      <th>29</th>
      <td>2.627911</td>
      <td>13.844820</td>
      <td>2.772829</td>
      <td>16.003843</td>
    </tr>
    <tr>
      <th>30</th>
      <td>2.617486</td>
      <td>13.701240</td>
      <td>2.769133</td>
      <td>15.944809</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>81</th>
      <td>2.347524</td>
      <td>10.459638</td>
      <td>2.667604</td>
      <td>14.405417</td>
    </tr>
    <tr>
      <th>82</th>
      <td>2.344474</td>
      <td>10.427790</td>
      <td>2.673300</td>
      <td>14.487702</td>
    </tr>
    <tr>
      <th>83</th>
      <td>2.341824</td>
      <td>10.400194</td>
      <td>2.676134</td>
      <td>14.528819</td>
    </tr>
    <tr>
      <th>84</th>
      <td>2.339246</td>
      <td>10.373411</td>
      <td>2.674447</td>
      <td>14.504333</td>
    </tr>
    <tr>
      <th>85</th>
      <td>2.335575</td>
      <td>10.335396</td>
      <td>2.671326</td>
      <td>14.459130</td>
    </tr>
    <tr>
      <th>86</th>
      <td>2.333568</td>
      <td>10.314676</td>
      <td>2.669627</td>
      <td>14.434579</td>
    </tr>
    <tr>
      <th>87</th>
      <td>2.330833</td>
      <td>10.286508</td>
      <td>2.677966</td>
      <td>14.555463</td>
    </tr>
    <tr>
      <th>88</th>
      <td>2.328374</td>
      <td>10.261241</td>
      <td>2.681782</td>
      <td>14.611103</td>
    </tr>
    <tr>
      <th>89</th>
      <td>2.324828</td>
      <td>10.224917</td>
      <td>2.681093</td>
      <td>14.601039</td>
    </tr>
    <tr>
      <th>90</th>
      <td>2.322852</td>
      <td>10.204739</td>
      <td>2.680783</td>
      <td>14.596520</td>
    </tr>
    <tr>
      <th>91</th>
      <td>2.320343</td>
      <td>10.179161</td>
      <td>2.665375</td>
      <td>14.373334</td>
    </tr>
    <tr>
      <th>92</th>
      <td>2.317596</td>
      <td>10.151238</td>
      <td>2.676520</td>
      <td>14.534432</td>
    </tr>
    <tr>
      <th>93</th>
      <td>2.315376</td>
      <td>10.128731</td>
      <td>2.682630</td>
      <td>14.623507</td>
    </tr>
    <tr>
      <th>94</th>
      <td>2.312999</td>
      <td>10.104688</td>
      <td>2.674479</td>
      <td>14.504788</td>
    </tr>
    <tr>
      <th>95</th>
      <td>2.310417</td>
      <td>10.078630</td>
      <td>2.675925</td>
      <td>14.525786</td>
    </tr>
    <tr>
      <th>96</th>
      <td>2.307760</td>
      <td>10.051883</td>
      <td>2.668844</td>
      <td>14.423290</td>
    </tr>
    <tr>
      <th>97</th>
      <td>2.305833</td>
      <td>10.032537</td>
      <td>2.672493</td>
      <td>14.476012</td>
    </tr>
    <tr>
      <th>98</th>
      <td>2.303393</td>
      <td>10.008078</td>
      <td>2.672982</td>
      <td>14.483100</td>
    </tr>
    <tr>
      <th>99</th>
      <td>2.301568</td>
      <td>9.989839</td>
      <td>2.677801</td>
      <td>14.553053</td>
    </tr>
    <tr>
      <th>100</th>
      <td>2.299155</td>
      <td>9.965758</td>
      <td>2.671343</td>
      <td>14.459382</td>
    </tr>
  </tbody>
</table>
</div>

[![loss-perplexity](/assets/img/loss-ppl.png)](/assets/img/loss-ppl.png)

The results above show that training for additional epochs continued to improve validation performance. The validation loss reached its lowest value of **2.665375** at epoch **90**. Although the improvement over earlier epochs was modest, it nevertheless indicates that the model had not yet fully converged.

The model appears to begin exhibiting mild overfitting after approximately 40 epochs, as the validation loss improves more slowly while the training loss continues to decrease. However, the training and validation perplexity curves remain close throughout training, suggesting that the model generalizes reasonably well to the validation data.
<br><br>

## Results
A prompt is passed to the model as a starting point for generating a story. The model generated the most coherent stories when prompted with "once upon a time." Examples generated using different *temperature* and *top-k* parameters are shown below. The most coherent results were obtained with **Temp = 0.5, Top-k = 50**.

**Greedy method:** *once upon a time little girl called lucy. she was very excited because today was going to the park with her mom. she was so excited to go to the park and she ran to the park. when they arrived, lucy saw a big tree with lots of leaves. she wanted to climb the tree and see what was there. she asked her mom if she could climb the tree and see the tree in the branches. her mom said yes, so lucy climbed up the tree and started climbing the tree. she climbed up the tree and started to climb. she was so*

**Temp = 0.8:** *once upon a time one day, a little girl named jane was very lonely. she was alone in her room and asked her mum if she could go outside. her mum said yes, so jane went to the garden and brought it there. he found a jar of jelly bean. she wanted to drink water and water it made her feel happy. jane watched as the jelly blossom shined in her sun, but she was too small. she frowned and said, "my mom and i'm full. i am lonely because you can't go to a lawyer i will give you another one with it."*

**Temp = 0.5, Top-k = 50:** *once upon a time little girl named lucy. she was very excited to go to the park with her mom. she saw a big slide and ran to it. when she got to the top, she saw a funny clown. he was juggling balls. lucy wanted to play too, so she ran up to the clown and shouted, "hi!" the clown smiled and said, "that is so cool! can we play with them?" lucy looked at the clown and said, "yes! let's play!" so lucy and the clown played together and had lots of fun. after a while, lucy got tired from playing with*

**Temp = 0.5, Top-k = 20:** *once upon a time little girl called sarah. she was very excited to learn something new. she put on her special adventure. she went to the store to buy something special. when sarah arrived at the store, sarah saw a big, colorful balloon. she wanted to try it on. she went to the store and bought the balloon. she was so excited! she put the balloon in her pocket and went to the park. she was so happy to have a new balloon. sarah was so excited to show her mom and they all clapped and cheered. after a few hours sarah was so*

While much of the generated text is not entirely coherent English, the stories often exhibit an underlying narrative structure despite occasional incorrect word choices and grammatical errors. This suggests that the model learned basic storytelling patterns even with its relatively small size and limited training data.

Without *top-k* sampling, coherence generally decreases as *temperature* increases and improves as it decreases. This trend is particularly evident when comparing greedy decoding with a temperature of 1.0. With *top-k* sampling enabled, the results suggest that achieving the best coherence requires balancing the *temperature* and *top-k* values.
