# Deep Learning Project - Text Generation
By Matthieu Serres & Youness ZAIM


## Motivation :

During this project, we found that text generation is one of the most popular application of Deep Learning and a lot of literature is available on the subject and it allowed us to avoid common mistakes. We used a Recurrent Neural Network model to generate a recipe and we used the data available at the following link:

https://storage.googleapis.com/recipe-box/recipes_raw.zip

We encountered some issues and difficulties that will be detailed below. 
More details are provided with the code in the ipynb file associated.

## Presentation of the data :

Our data regroups 3 files that we merged and cleaned in order to get recipes with :\

•	A title \
•	A list of ingredients with proportions \
•	Some cooking instructions \

After cleaning and merging the files, we obtained a data set with 122917 different recipes.
This plenty enough to build a decent text generating model.

Here is an example of a random recipe in our data set :
![re](https://user-images.githubusercontent.com/25570567/103688469-20db6200-4f92-11eb-81ba-e412dec12de8.png)

## Building the model

In order to create our model, we used a RNN defined by tf.keras.Sequential. Our model works at the character level which means that given a character in the recipe, it will generate the next character.
We used 3 layers in this model:\
•	**Tf.keras.layers.Embedding** : Embedding layer as the input layer \
•	**Tf.keras.layers.LSTM** : LSTM layer which is performant for processing natural language \
•	**Tf.keras.layers.Dense** : Dense layer as the output layer which generates logits predicting the log-likelihood of the next character. \
We choose the LSTM layer because the recipes are written in natural language which well handled by LSTM.

### This the summuray of our model :

<img width="571" alt="Capture d’écran 2021-01-05 à 19 31 40" src="https://user-images.githubusercontent.com/25570567/103684964-b3790280-4f8c-11eb-9e26-d5b45b39f86b.png">

### This the visualization of our model :

![tt](https://user-images.githubusercontent.com/25570567/103685128-f89d3480-4f8c-11eb-879d-5e35ec1f4094.png)

For each character the model looks up the embedding, runs the LSTM one time-step with the embedding as input, and applies the dense layer to generate logits predicting the log-likelihood of the next character:

![0_GxVDsOcMjVp07Lrn](https://user-images.githubusercontent.com/25570567/103685501-a3155780-4f8d-11eb-912f-7cc88988c57e.png)

The picture above illustrates GRU network, but we can replace GRU with LSTM.

### The optimizer the loss function :

We chose the ADAM optimizer using  **Tf.keras.optimizers.Adam** optimizer with the sparse categorical cross entropy as a loss function using **Tf.keras.losses.sparse_categorical_crossentropy()**

### Training the model :
We trained our model for 50 epochs with 20 steps per epoch. This led us to bad results but is was necessary to have an acceptable training time. **This didn't enable us to present some useful statistics in our training** (A proper training needs more than 4 days of traning to get good results).
In addition we created a model checkpoints callback.  This let's configure a **tf.keras.callbacks.ModelCheckpoint** checkpoint that will allow us to periodically save trained weights to the file so that we could restore the model from weights afterwards.

### Generating recipes :

First, we rebuild the prediction model with a batch size of 1 in order to keep the prediction process simpler and we load the wheights that we got after the training.\
To use our trained model for recipe generation we need to implement a so-called **prediction loop**. The following code block generates the text using the loop:\
• Begins by choosing a start string, initializing the RNN state and setting the number of characters to generate..\
• Gets the prediction distribution of the next character using the start string and the RNN state..\
• Uses a categorical distribution to calculate the index of the predicted character. Use this predicted character as our next input to the model.\
• The RNN state returned by the model is fed back into the model so that it now has more context, instead of only one character. After predicting the next character, the modified RNN states are again fed back into the model, which is how it learns as it gets more context from the previously predicted characters.\

The **temperature** parameter defines unexpectency in the generated recipe. Generally, low temperatures results in more predictable text while higher temperatures result in more surprising text.

![0_glH5Eo3QaA9x1_uF](https://user-images.githubusercontent.com/25570567/103687151-19b35480-4f90-11eb-8a83-0805006cc962.png)

Next,using our **generate_text()** function to  generate some new recipes, we set the temperature for prediction loop to **0.2** as the lower the temperature, the more coherent, the result.

### Building a Text Generator Web App :

Using **Panel** as the web dashboard, we designed an interactive model demo to play around with this model, input text, and the receipe lenght to generate some random recipes right in your browser ! \
https://user-images.githubusercontent.com/25570567/103687613-dc9b9200-4f90-11eb-982f-bf0fe9cf1b44.mp4


## Conclusion :

As we have seen in this report, we could build a working RNN model that correctly generates a recipe from a key ingredient. However, we struggled to make it efficient and to keep the training time low. We might have been limited either by our hardware or software and to train our model with optimal parameters would have taken at least three days. Thus, we decided to settle for parameters that only give bad result.

The main reason for this is that we had to limit drastically the length of the recipes in order to save time and memory, which left us with only 21 recipes in our data set. This is obviously way too low to build a working model, no matter what model we choose.

However, some ideas to improve our model exist and we couldn’t implement them for a lack of time or resources. First, we could use more powerful computers as ours are not really designed to handle such a quantity of data.

A second option to consider would be to build a model that processes recipes word by word and not character by character as it is the case in our project. Indeed, it could be easier to handle for our computers as it would represent less data. 


