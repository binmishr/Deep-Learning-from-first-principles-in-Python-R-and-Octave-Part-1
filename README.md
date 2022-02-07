# Deep-Learning-from-first-principles-in-Python-R-and-Octave-Part-1

The details of the codeset and plots are included in the attached Microsoft Word Document (.docx) file in this repository. 
You need to view the file in "Read Mode" to see the contents properly after downloading the same.

A Brief Introduction
======================

This 2 layer network is implemented in Python, R and Octave languages. I have included Octave, into the mix, as Octave is a close cousin of Matlab. These implementations in Python, R and Octave are equivalent vectorized implementations. So, if you are familiar in any one of the languages, you should be able to look at the corresponding code in the other two.

To start with, Logistic Regression is performed using sklearn's logistic regression package for the cancer data set also from sklearn. This is shown below

## 1. Logistic Regression
```{python}
import numpy as np
import pandas as pd
import os
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.datasets import make_classification, make_blobs
from sklearn.metrics import confusion_matrix
from matplotlib.colors import ListedColormap
from sklearn.datasets import load_breast_cancer
# Load the cancer data
(X_cancer, y_cancer) = load_breast_cancer(return_X_y = True)
#Split into train and test sets
X_train, X_test, y_train, y_test = train_test_split(X_cancer, y_cancer,
                                                   random_state = 0)
# Call the Logisitic Regression function
clf = LogisticRegression().fit(X_train, y_train)
print('Accuracy of Logistic regression classifier on training set: {:.2f}'
     .format(clf.score(X_train, y_train)))
print('Accuracy of Logistic regression classifier on test set: {:.2f}'
     .format(clf.score(X_test, y_test)))
```

## 2. Logistic Regression as a 2 layer Neural Network
In the following section Logistic Regression is implemented as a 2 layer Neural Network in Python, R and Octave. The same cancer data set from sklearn will be used to train and test the Neural Network in Python, R and Octave.

The cancer data set has 30 input features, and the target variable 'output' is either 0 or 1. Hence the sigmoid activation function will be used in the output layer for classification.

This simple 2 layer Neural Network is shown below

At the input layer there are 30 features and the corresponding weights of these inputs which are initialized to small random values. 
Z= w_{1}x_{1} +w_{2}x_{2} +...+ w_{30}x_{30} + b

where 'b' is the bias term

The Activation function is the sigmoid function which is
\a = 1/(1+e^{-z})

The Loss, when the sigmoid function is used in the output layer, is given by
L=-(y*log(a) + (1-y)*log(1-a))   (1)

**Gradient Descent**

### Forward propagation
In forward propagation cycle of the Neural Network the output Z and the output of activation function, the  sigmoid function, is first computed. Then using the output 'y' for the given features, the 'Loss' is computed using equation (1) above.  

### Backward propagation
The backward propagation cycle determines how the 'Loss' is impacted for small variations from the previous layers upto the input layer. In other words, backward propagation computes the changes in the weights at the input layer, which will minimize the loss. Several cycles of gradient descent are performed in the path of steepest descent to find the local minima. In other words the set of weights at the input layer, which will result in the lowest loss. The weights at the input layer are decreased by a parameter known as the 'learning rate'. Too big a 'learning rate' can overshoot the local minima and too small a 'learning rate' can take a long time to reach the local minima. This is done for 'm' training examples. 

Some important derivations that are essential for back propagation
<strong>Chain rule of differentiation</strong>
Let y=f(u)
and u=g(x) then
latex \partial y/\partial x = \partial y/\partial u * \partial u/\partial x
d/dx(logx)=1/x

<strong>An important result</strong>
latex a=1/(1+e^{-z})
Let latex x= 1 + e^{-z}  then
latex y = 1/x
latex \partial y/\partial x = -1/x^{2}
latex \partial x/\partial z = -e^{-z}
d/d

Using the chain rule of differentiation we get
latex \partial y/\partial z = \partial y/\partial x * \partial x/\partial z$
latex =-1/(1+e^{-z})^{2}* -e^{-z} = e^{-z}/(1+e^{-z})^{2}$
Therefore latex \partial y/\partial z = y(1-y)$  -(2)

The 3 equations for the 2 layer Neural Network representation of Logistic Regression are

L=-(y*log(a) + (1-y)*log(1-a))  -(a)
\a = 1/(1+e^{-z}) -(b)
Z= w_{1}x_{1} +w_{2}x_{2} +...+ w_{30}x_{30} +b  --(c)

Computing the derivatives for back propagation we have

dL/da = -(y/a + (1-y)/(1-a)) since d/dx(logx) = 1/x  -(d)

Also from equation 2 we get
dA/dZ = a (1-a)  - (e)

Since
\partial L/\partial Z = \partial L/\partial A * \partial A/\partial Z

and substituing the results of (d) & (e) we get
\partial L/\partial Z = -(y/a + (1-y)/(1-a)) * a(1-a) = a-y (f)

Finally
\partial L/\partial w_{i}= \partial L/\partial A * \partial A/\partial Z * \partial Z/\partial w_{i} -(g)

\partial Z/\partial w_{i} = x_{i} - (h)

Subsituting  the result in equation (h) in equation (g) we get
\partial L/\partial w_{i} = x_{i}* (a-y) -(i)

Also 
\partial L/\partial b = \partial L/\partial A * \partial A/\partial Z * \partial Z/\partial b -(j)

Since
\partial Z/\partial b = 1

\partial L/\partial b = a-y

## 3. Vectorized implementation of Neural Network for Logistic Regression -Python code

```{python}
import numpy as np
import pandas as pd
import os
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
# Define the sigmoid function
def sigmoid(z):  
    a=1/(1+np.exp(-z))    
    return a
# Initialize
def initialize(dim):
    w = np.zeros(dim).reshape(dim,1)
    b = 0   
    return w
# Compute the loss
def computeLoss(numTraining,Y,A):
    loss=-1/numTraining *np.sum(Y*np.log(A) + (1-Y)*(np.log(1-A)))
    return(loss)
# Execute the forward propagation
def forwardPropagation(w,b,X,Y):
    # Compute Z
    Z=np.dot(w.T,X)+b
    # Determine the number of training samples
    numTraining=float(len(X))
    # Compute the output of the sigmoid activation function 
    A=sigmoid(Z)
    #Compute the loss
    loss = computeLoss(numTraining,Y,A)
    # Compute the gradients dZ, dw and db
    dZ=A-Y
    dw=1/numTraining*np.dot(X,dZ.T)
    db=1/numTraining*np.sum(dZ)
    
    # Return the results as a dictionary
    gradients = {"dw": dw,
             "db": db}
    loss = np.squeeze(loss)
    return gradients,loss
# Compute Gradient Descent    
def gradientDescent(w, b, X, Y, numIerations, learningRate):
    losses=[]
    idx =[]
    # Iterate 
    for i in range(numIerations):
        gradients,loss=forwardPropagation(w,b,X,Y)
        #Get the derivates
        dw = gradients["dw"]
        db = gradients["db"]
        w = w-learningRate*dw
        b = b-learningRate*db
        # Store the loss
        if i % 100 == 0:
            idx.append(i)
            losses.append(loss)      
        # Set params and grads
        params = {"w": w,
                  "b": b}  
        grads = {"dw": dw,
                 "db": db}
    
    return params, grads, losses,idx
# Predict the output for a training set 
def predict(w,b,X):
    size=X.shape[1]
    yPredicted=np.zeros((1,size))
    Z=np.dot(w.T,X)
    # Compute the sigmoid
    A=sigmoid(Z)
    for i in range(A.shape[1]):
        #If the value is > 0.5 then set as 1
        if(A[0][i] > 0.5):
            yPredicted[0][i]=1
        else:
        # Else set as 0
            yPredicted[0][i]=0
    return yPredicted
#Normalize the data   
def normalize(x):
    x_norm = None
    x_norm = np.linalg.norm(x,axis=1,keepdims=True)
    x= x/x_norm
    return x
   
# Run the 2 layer Neural Network on the cancer data set
from sklearn.datasets import load_breast_cancer
# Load the cancer data
(X_cancer, y_cancer) = load_breast_cancer(return_X_y = True)
# Create train and test sets
X_train, X_test, y_train, y_test = train_test_split(X_cancer, y_cancer,
                                                   random_state = 0)
# Normalize the data for better performance
X_train1=normalize(X_train)
# Create weight vectors of zeros. The size is the number of features in the data set=30
w=np.zeros((X_train.shape[1],1))
#w=np.zeros((30,1))
b=0
#Normalize the training data so that gradient descent performs better
X_train1=normalize(X_train)
#Transpose X_train so that we have a matrix as (features, numSamples)
X_train2=X_train1.T
# Reshape to remove the rank 1 array and then transpose
y_train1=y_train.reshape(len(y_train),1)
y_train2=y_train1.T
# Run gradient descent for 4000 times and compute the weights
parameters, grads, costs,idx = gradientDescent(w, b, X_train2, y_train2, numIerations=4000, learningRate=0.75)
w = parameters["w"]
b = parameters["b"]
   
# Normalize X_test
X_test1=normalize(X_test)
#Transpose X_train so that we have a matrix as (features, numSamples)
X_test2=X_test1.T
#Reshape y_test
y_test1=y_test.reshape(len(y_test),1)
y_test2=y_test1.T
# Predict the values for 
yPredictionTest = predict(w, b, X_test2)
yPredictionTrain = predict(w, b, X_train2)
# Print the accuracy
print("train accuracy: {} %".format(100 - np.mean(np.abs(yPredictionTrain - y_train2)) * 100))
print("test accuracy: {} %".format(100 - np.mean(np.abs(yPredictionTest - y_test)) * 100))
# Plot the Costs vs the number of iterations
fig1=plt.plot(idx,costs)
fig1=plt.title("Gradient descent-Cost vs No of iterations")
fig1=plt.xlabel("No of iterations")
fig1=plt.ylabel("Cost")
fig1.figure.savefig("fig1", bbox_inches='tight')
```


## 4. Vectorized implementation of Neural Network for Logistic Regression -R code
```{r fig 1,cache=TRUE}
source("RFunctions-1.R")
# Define the sigmoid function
sigmoid <- function(z){
    a <- 1/(1+ exp(-z))
    a
}
# Compute the loss
computeLoss <- function(numTraining,Y,A){
    loss <- -1/numTraining* sum(Y*log(A) + (1-Y)*log(1-A))
    return(loss)
}
# Compute forward propagation
forwardPropagation <- function(w,b,X,Y){
    # Compute Z
    Z <- t(w) %*% X +b
    #Set the number of samples
    numTraining <- ncol(X)
    # Compute the activation function
    A=sigmoid(Z) 
    
    #Compute the loss
    loss <- computeLoss(numTraining,Y,A)
    
    # Compute the gradients dZ, dw and db
    dZ<-A-Y
    dw<-1/numTraining * X %*% t(dZ)
    db<-1/numTraining*sum(dZ)
    
    fwdProp <- list("loss" = loss, "dw" = dw, "db" = db)
    return(fwdProp)
}
# Perform one cycle of Gradient descent
gradientDescent <- function(w, b, X, Y, numIerations, learningRate){
    losses <- NULL
    idx <- NULL
    # Loop through the number of iterations
    for(i in 1:numIerations){
        fwdProp <-forwardPropagation(w,b,X,Y)
        #Get the derivatives
        dw <- fwdProp$dw
        db <- fwdProp$db
        #Perform gradient descent
        w = w-learningRate*dw
        b = b-learningRate*db
        l <- fwdProp$loss
        # Stoe the loss
        if(i %% 100 == 0){
            idx <- c(idx,i)
            losses <- c(losses,l)  
        }
    }
    
    # Return the weights and losses
    gradDescnt <- list("w"=w,"b"=b,"dw"=dw,"db"=db,"losses"=losses,"idx"=idx)
   
    
    return(gradDescnt)
}
# COmpute the predicted value for input
predict <- function(w,b,X){
    m=dim(X)[2]
    # Create a ector of 0's
    yPredicted=matrix(rep(0,m),nrow=1,ncol=m)
    Z <- t(w) %*% X +b
    # Compute sigmoid
    A=sigmoid(Z)
    for(i in 1:dim(A)[2]){
        # If A > 0.5 set value as 1
        if(A[1,i] > 0.5)
        yPredicted[1,i]=1
       else
        # Else set as 0
        yPredicted[1,i]=0
    }
    return(yPredicted)
}
# Normalize the matrix
normalize <- function(x){
    #Create the norm of the matrix.Perform the Frobenius norm of the matrix 
    n<-as.matrix(sqrt(rowSums(x^2)))
    #Sweep by rows by norm. Note '1' in the function which performing on every row
    normalized<-sweep(x, 1, n, FUN="/")
    return(normalized)
}
# Run the 2 layer Neural Network on the cancer data set
# Read the data (from sklearn)
cancer <- read.csv("cancer.csv")
# Rename the target variable
names(cancer) <- c(seq(1,30),"output")
# Split as training and test sets
train_idx <- trainTestSplit(cancer,trainPercent=75,seed=5)
train <- cancer[train_idx, ]
test <- cancer[-train_idx, ]
# Set the features
X_train <-train[,1:30]
y_train <- train[,31]
X_test <- test[,1:30]
y_test <- test[,31]
# Create a matrix of 0's with the number of features
w <-matrix(rep(0,dim(X_train)[2]))
b <-0
X_train1 <- normalize(X_train)
X_train2=t(X_train1)
# Reshape  then transpose
y_train1=as.matrix(y_train)
y_train2=t(y_train1)
# Perform gradient descent
gradDescent= gradientDescent(w, b, X_train2, y_train2, numIerations=3000, learningRate=0.77)
# Normalize X_test
X_test1=normalize(X_test)
#Transpose X_train so that we have a matrix as (features, numSamples)
X_test2=t(X_test1)
#Reshape y_test and take transpose
y_test1=as.matrix(y_test)
y_test2=t(y_test1)
# Use the values of the weights generated from Gradient Descent
yPredictionTest = predict(gradDescent$w, gradDescent$b, X_test2)
yPredictionTrain = predict(gradDescent$w, gradDescent$b, X_train2)
sprintf("Train accuracy: %f",(100 - mean(abs(yPredictionTrain - y_train2)) * 100))
sprintf("test accuracy: %f",(100 - mean(abs(yPredictionTest - y_test)) * 100))
df <-data.frame(gradDescent$idx, gradDescent$losses)
names(df) <- c("iterations","losses")
ggplot(df,aes(x=iterations,y=losses)) + geom_point() + geom_line(col="blue") +
    ggtitle("Gradient Descent - Losses vs No of Iterations") +
    xlab("No of iterations") + ylab("Losses")
```
