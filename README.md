# FSML
A machine learning project written in F#

### Version
0.1.2

### Algorithms implemented:
  - Linear Regression
    * no penalty
    * L1 penalty via coordinate descent
    * L2 penalty
  - Logistic Regression
    * no penalty
    * L1 penalty via coordinate descent
    * L2 penalty via iterated reweighted least square
  - SVM via Sequential Minimal Optimization (SMO)
    * linear kernel
    * rbf(Gaussian) kernel 

### Algorithms todo list:
  - cox ph model
  - subdistribution model

# Examples


## 1. Linear regression

We use the data /data/continuous.txt, which is stored in the libsvm format. Please note that no imputation is implemented at this time so missing values in the data file would throw exceptions.


```fsharp
open DataTypes
open LinearRegression
open Utilities
[<EntryPoint>]
let main argv = 
    let dat= new readData (@"/data/continuous.txt", "continuous")
    let seed=1 // random seed
    let folds=3 // split data into 3 folds
    let datFold = new data (dat.CreateFold folds seed ,dat.Features) // prepare data
    let fold=1 // use fold 1 for test and the others for train
    let xTrain,yTrain= datFold.Train fold
    // in case to train the model using all data:
    //let xTrain,yTrain= datFold.All 
    let xTest,yTest= datFold.Test fold

    // train a standard linear regression
    let lm= new LM(xTrain,yTrain)
    do lm.Fit()
    let pTrain = lm.Predict xTrain // make prediction on training data
    let pTest = lm.Predict xTest // make prediction on testing data
    let rmseTrain=RMSE yTrain pTrain // compute RMSE on training data
    let rmseTest=RMSE yTest pTest // compute RMSE on testing data
    printfn "train rmse: %A" rmseTrain
    printfn "test  rmse: %A" rmseTest
    printfn "beta: %A" (lm.Beta.ToArray())

    // train a linear regression with L2 penalty, i.e., ridge linear regression
    let lml2= new LMRidge(xTrain,yTrain,0.2) // lambda = 0.2 is the penalty parameter
    do lml2.Fit()
    let pTrainl2 = lml2.Predict xTrain // make prediction on training data
    let pTestl2 = lml2.Predict xTest // make prediction on testing data
    let rmseTrainl2=RMSE yTrain pTrainl2 // compute RMSE on training data
    let rmseTestl2=RMSE yTest pTestl2 // compute RMSE on testing data
    printfn "train rmse: %A" rmseTrainl2
    printfn "test  rmse: %A" rmseTestl2
    printfn "beta: %A" (lml2.Beta.ToArray())

    // train a linear regression with L1 penalty, i.e., lasso linear regression
    let lml1= new LMLasso(xTrain,yTrain,0.2) // lambda = 0.2 is the penalty parameter
    do lml1.Fit()
    let pTrainl1 = lml1.Predict xTrain // make prediction on training data
    let pTestl1 = lml1.Predict xTest // make prediction on testing data
    let rmseTrainl1=RMSE yTrain pTrainl1 // compute RMSE on training data
    let rmseTestl1=RMSE yTest pTestl1 // compute RMSE on testing data
    printfn "train rmse: %A" rmseTrainl1
    printfn "test  rmse: %A" rmseTestl1
    printfn "beta: %A" (lml1.Beta.ToArray())
```


## 2. Logistic regression

We use the data /data/binary.txt, which is stored in the libsvm format.
