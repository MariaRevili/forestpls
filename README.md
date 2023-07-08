# Forest-PLS Algorithm


## The paper can be found at: https://arxiv.org/pdf/2301.00251.pdf


The code contains:

simulation.R - for replicating the simulated experiments in the main part of the paper.

forest_pls_theoretical_simulations.R - for replicating the theoretical simulations in the Appendix.

reemployment_experiment.R - for replicating the application in the main part of the paper. 

The proposed algorithm can be run as: 

```
forest_pls <- function(Y, D, X){
  
  
  sim_linear <- plsr(Y~X, scale=TRUE, validation="CV")
  ### Extract scores (components)
  X_scores <- as.data.frame(sim_linear$scores[, c(1, 2)])
  names(X_scores)[c(1, 2)] <- c("c1", "c2")
  X_scores <- as.matrix(X_scores)
  
  
  ### Run random forests for heterogeneity
  #install.packages("grf")
  library(grf)
  causal_model <- causal_forest(X_scores, Y, D, num.trees = 1000, seed = 0)
  oob_predictions <- predict(causal_model, estimate.variance = TRUE)
  head(oob_predictions)
  
  covariates_all <- as.data.frame(X)
  covariates_all$ate <- oob_predictions$predictions
  covariates_all$ate_var <- oob_predictions$variance.estimates
  
  covariates_all <- cbind(covariates_all, X_scores)

  
  return(covariates_all)
  
}

```






