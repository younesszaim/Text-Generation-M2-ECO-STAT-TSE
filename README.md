# Text-Generation-M2-ECO-STAT-TSE
Projet for Deep Learning 2 course about Generating cooking recipes


## Motivation :

Deep Learning has the ability to widely transform our lives by enabling us to create complex objects such as search engines but also with much simpler applications like text generation.

This kind of simpler applications is particularly interesting because despite it’s apparent simplicity, it could impact our daily life. 

We started from this simple observation and decided to create an application that can generate a recipe if you give it an ingredient. This is exactly the type of application that could be used daily.

In this project, we used a Recurrent Neural Network model to generate a recipe. We used the data available at the following link:

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
We trained our model for 50 epochs with 20 steps per epoch. This led us to bad results but is was necessary to have an acceptable training time.(More than 4 days of traning to get good results).
In addition we created a model checkpoints callback.  This let's configure a **tf.keras.callbacks.ModelCheckpoint** checkpoint that will allow us to periodically save trained weights to the file so that we could restore the model from weights afterwards.

### Generating recipes :

First, we rebuild the prediction model with a batch size of 1 in order to keep the prediction process simpler and we load the wheights that we got after the training.\
To use our trained model for recipe generation we need to implement a so-called **prediction loop**. The following code block generates the text using the loop:\
• It starts by choosing a start string, initializing the RNN state and setting the number of characters to generate.\
• It gets the prediction distribution of the next character using the start string, and the RNN state.\
• Then, it uses a categorical distribution to calculate the index of the predicted character. It uses this predicted character as the next input to the model.\
• The RNN state returned by the model is fed back into the model so that it now has more context, instead of only one character. After predicting the next character, the modified RNN states are again fed back into the model, which is how it learns as it gets more context from the previously predicted characters.\
The **temperature** parameter here defines how fuzzy or how unexpected the generated recipe is going to be. Low temperatures results in more predictable text. Higher temperatures result in more surprising text. You need to experiment to find the best setting. We will do some experimentation with different temperatures below.

![0_glH5Eo3QaA9x1_uF](https://user-images.githubusercontent.com/25570567/103687151-19b35480-4f90-11eb-8a83-0805006cc962.png)

Next,using our **generate_text()** function to  generate some new recipes, we set the temperature for prediction loop to **0.2**.

### Building a Text Generator Web App :

Using **Panel** as the web dashboard, we designed an interactive model demo to play around with this model, input text, and the receipe lenght to generate some random recipes right in your browser ! \
https://user-images.githubusercontent.com/25570567/103687613-dc9b9200-4f90-11eb-982f-bf0fe9cf1b44.mp4



