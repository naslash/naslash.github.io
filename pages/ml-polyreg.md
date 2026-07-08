---
layout: page
title: "Machine Learning: Predicting Fantasy Points using Polynomial Regression"
permalink: /polyreg
---

For this project, I use a *polynomial regression* model to predict a player's fantasy football points. To predict fantasy points before a game is played, a model would first need to predict the player's individual statistics, such as passing yards, rushing touchdowns, and receptions. Instead, this model uses the actual statistics as input to predict the final fantasy point total. While this is a simpler prediction task, my primary goal is to build a strong understanding of the fundamentals since this is my first machine learning project. This approach also makes it easier to validate the model's predictions against the known fantasy point totals.

## About the data
The data was collected from Sports Reference, which provides comprehensive historical football statistics. Much of the statistical data is compiled from official sources, including the NFL. This project uses NFL player statistics from 2012---2023 for training and evaluates the model using the official 2024 statistics, which remain unseen during training, to measure how accurately it predicts fantasy points.

I wrote a Python script to scrape the required data, clean it, and merge it into a *CSV* file. For scraping, I use BeautifulSoup to extract player data for the selected features and positions. Only offensive positions are included: quarterback, running back, wide receiver, tight end, and fullback. The *position* feature has been one-hot encoded since all four are merged into the same dataset.

The extracted features include: player name, position, age, games played, games started, passes completed, passing yards, passing touchdowns, interceptions thrown, rushing attempts, rushing yards gained, rushing touchdowns, receptions, receiving yards, receiving touchdowns, fumbles lost, and fantasy points.

## Model Description
A *polynomial regression* model was trained and evaluated using the collected data. The model was tested using polynomial degrees of 2, 3, 4, and 5. Performance on the training and testing sets was compared to detect overfitting or underfitting. Model performance was measured using the *Root Mean Squared Error* (RMSE). I also used scikit-learn's `cross_val_score` function to determine the optimal polynomial degree and evaluated a *ridge regression* model to determine whether regularization could improve performance.

## Training and Validating
The table below displays the initial *RMSE* values for each polynomial degree. The best performance is achieved with a polynomial degree of 2, and higher degrees begin to exhibit overfitting.

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
      <th>Degree</th>
      <th>RMSE Train</th>
      <th>RMSE Test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>2</td>
      <td>3.959906e-02</td>
      <td>0.017283</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3.593902e-02</td>
      <td>12.529721</td>
    </tr>
    <tr>
      <td>4</td>
      <td>3.399018e-09</td>
      <td>54.622315</td>
    </tr>
    <tr>
      <td>5</td>
      <td>6.792445e-11</td>
      <td>9.294567</td>
    </tr>
  </tbody>
</table>
</div>

Before concluding that a polynomial degree of 2 provides the best performance, I verified the result using Scikit-learn's `cross_val_score` function. Since the previous results indicate that overfitting begins at degree 3, and because higher polynomial degrees require significantly longer execution times, *cross-validation* was performed only on degrees 2, 3, and 4. This provides a more reliable estimate of the optimal polynomial degree while keeping the test data completely unseen until the final evaluation.

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
      <th>Degree</th>
      <th>RMSE: mean</th>
      <th>k = 1</th>
      <th>k = 2</th>
      <th>k = 3</th>
      <th>k = 4</th>
      <th>k = 5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>2</td>
      <td>-0.033886</td>
      <td>-0.102867</td>
      <td>-0.015656</td>
      <td>-0.016688</td>
      <td>-0.013699</td>
      <td>-0.020518</td>
    </tr>
    <tr>
      <td>3</td>
      <td>-55.030177</td>
      <td>-10.432921</td>
      <td>-40.548514</td>
      <td>-146.339178</td>
      <td>-20.210193</td>
      <td>-57.620079</td>
    </tr>
    <tr>
      <td>4</td>
      <td>-52.724258</td>
      <td>-20.536799</td>
      <td>-55.021867</td>
      <td>-141.742147</td>
      <td>-18.813517</td>
      <td>-27.506963</td>
    </tr>
  </tbody>
</table>
</div>

*Cross-validation* confirms that a polynomial degree of 2 is optimal for this model, although further improvements may still be possible. Since the model begins to overfit at degree 3, *ridge regression* was evaluated to determine whether regularization could improve performance. It was also evaluated with a polynomial degree of 2 to determine whether regularization could further improve performance.

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
      <th>Alpha (Degree 2)</th>
      <th>RMSE Train</th>
      <th>RMSE Test</th>
      <th>Alpha (Degree 3)</th>
      <th>RMSE Train</th>
      <th>RMSE Test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0.000010</td>
      <td>0.039603</td>
      <td>0.017047</td>
      <td>0.000010</td>
      <td>0.036635</td>
      <td>0.667652</td>
    </tr>
    <tr>
      <td>0.000034</td>
      <td>0.039612</td>
      <td>0.016878</td>
      <td>0.000034</td>
      <td>0.036856</td>
      <td>0.472239</td>
    </tr>
    <tr>
      <td>0.000113</td>
      <td>0.039639</td>
      <td>0.016816</td>
      <td>0.000113</td>
      <td>0.037087</td>
      <td>0.396696</td>
    </tr>
    <tr>
      <td>0.000379</td>
      <td>0.039723</td>
      <td>0.017174</td>
      <td>0.000379</td>
      <td>0.037312</td>
      <td>0.310072</td>
    </tr>
    <tr>
      <td>0.001274</td>
      <td>0.040011</td>
      <td>0.018783</td>
      <td>0.001274</td>
      <td>0.037603</td>
      <td>0.218112</td>
    </tr>
    <tr>
      <td>0.004281</td>
      <td>0.040765</td>
      <td>0.022851</td>
      <td>0.004281</td>
      <td>0.038157</td>
      <td>0.147271</td>
    </tr>
    <tr>
      <td>0.014384</td>
      <td>0.042717</td>
      <td>0.032320</td>
      <td>0.014384</td>
      <td>0.039454</td>
      <td>0.127369</td>
    </tr>
    <tr>
      <td>0.048329</td>
      <td>0.048848</td>
      <td>0.052699</td>
      <td>0.048329</td>
      <td>0.042847</td>
      <td>0.185173</td>
    </tr>
    <tr>
      <td>0.162378</td>
      <td>0.063314</td>
      <td>0.079877</td>
      <td>0.162378</td>
      <td>0.053372</td>
      <td>0.282362</td>
    </tr>
    <tr>
      <td>0.545559</td>
      <td>0.095987</td>
      <td>0.118607</td>
      <td>0.545559</td>
      <td>0.080873</td>
      <td>0.364976</td>
    </tr>
    <tr>
      <td>1.832981</td>
      <td>0.173442</td>
      <td>0.205185</td>
      <td>1.832981</td>
      <td>0.136381</td>
      <td>0.389988</td>
    </tr>
    <tr>
      <td>6.158482</td>
      <td>0.336721</td>
      <td>0.382432</td>
      <td>6.158482</td>
      <td>0.243059</td>
      <td>0.472138</td>
    </tr>
  </tbody>
</table>
</div>

[![ridge-reg](/assets/img/ridgereg.png)](/assets/img/ridgereg.png)

These results show that although performance improves at degree 3, it still does not outperform a polynomial degree of 2. It also exhibits slight overfitting at lower alpha values. Performance also improves at a polynomial degree of 2 when *ridge regression* is applied, with the optimal alpha appearing to be approximately 0.000113. As a result, the RMSE decreases from 0.017283 to 0.016816. Beyond this point, the model begins to underfit as the regularization strength increases.

Before proceeding with this alpha value, just as Scikit-learn's `cross_val_score` was used to select the polynomial degree, `GridSearchCV` was used to select the optimal *Ridge Regression* alpha. It was applied to the training set, and the selected alpha was compared with the previous results to verify that the optimal value had been chosen. Also like `cross_val_score`, `GridSearchCV` evaluates multiple alpha values while keeping the test data completely unseen.  

Using `GridSearchCV` with a polynomial degree of 2 revealed that 0.000034 is the optimal alpha rather than 0.000113 as shown above. Although an alpha of 0.000113 produces a lower *RMSE* on this particular test set, selecting hyperparameters based on the test set risks overfitting to that specific data. Because `GridSearchCV` selects the alpha using cross-validation on the training data alone, it provides a more reliable estimate of the optimal value for unseen data. Therefore, an alpha of 0.000034 was chosen.

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
      <th>Degree</th>
      <th>Alpha</th>
      <th>RMSE Train</th>
      <th>RMSE Test</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>2</td>
      <td>0.000034</td>
      <td>0.039612</td>
      <td>0.016878</td>
    </tr>
  </tbody>
</table>
</div>

Now that the optimal hyperparameters have been chosen, the model can be evaluated using the test set, where it predicts fantasy points with a high degree of accuracy.

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
      <th></th>
      <th>Player</th>
      <th>Test Values</th>
      <th>Predicted Values</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Saquon Barkley</td>
      <td>355.3</td>
      <td>355.314278</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Derrick Henry</td>
      <td>336.4</td>
      <td>336.424169</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Jahmyr Gibbs</td>
      <td>362.9</td>
      <td>362.892506</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Lamar Jackson</td>
      <td>430.4</td>
      <td>430.452023</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ja'Marr Chase</td>
      <td>403.0</td>
      <td>402.998907</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Bijan Robinson</td>
      <td>341.7</td>
      <td>341.713933</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Josh Jacobs</td>
      <td>293.1</td>
      <td>293.105874</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Josh Allen</td>
      <td>379.0</td>
      <td>379.082981</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Joe Burrow</td>
      <td>372.8</td>
      <td>372.81056</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Kyren Williams</td>
      <td>272.1</td>
      <td>272.108287</td>
    </tr>
    <tr>
      <th>10</th>
      <td>James Cook</td>
      <td>266.7</td>
      <td>266.710266</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Justin Jefferson</td>
      <td>317.5</td>
      <td>317.48477</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Baker Mayfield</td>
      <td>365.8</td>
      <td>365.78426</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Jonathan Taylor</td>
      <td>244.7</td>
      <td>244.685462</td>
    </tr>
    <tr>
      <th>14</th>
      <td>George Kittle</td>
      <td>236.6</td>
      <td>236.60158</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Jayden Daniels</td>
      <td>355.8</td>
      <td>355.821292</td>
    </tr>
    <tr>
      <th>16</th>
      <td>De'Von Achane</td>
      <td>299.9</td>
      <td>299.905611</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Amon-Ra St. Brown</td>
      <td>316.2</td>
      <td>316.195972</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Brian Thomas</td>
      <td>284.0</td>
      <td>284.007062</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Brock Bowers</td>
      <td>262.7</td>
      <td>262.700865</td>
    </tr>
    <tr>
      <th>20</th>
      <td>James Conner</td>
      <td>253.8</td>
      <td>253.80399</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Joe Mixon</td>
      <td>240.5</td>
      <td>240.496855</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Terry McLaurin</td>
      <td>267.8</td>
      <td>267.800429</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Trey McBride</td>
      <td>249.8</td>
      <td>249.807507</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Chase Brown</td>
      <td>255.0</td>
      <td>255.001714</td>
    </tr>
  </tbody>
</table>
</div>

## Conclusion
Overall, the *polynomial ridge regression* model produces the lowest *RMSE*. A previously tested linear regression model (polynomial degree 1) came close, suggesting that the relationship between the features and fantasy points is not entirely linear. One possible explanation is that the five player positions have different fantasy point distributions, introducing nonlinear relationships that a polynomial model is better able to capture.

The following histograms show the distribution of fantasy points by player position. Both use the same data, however, one displays the raw counts, while the other is normalized.

[![histogram](/assets/img/polyreg-hist.png)](/assets/img/polyreg-hist.png)

As the histograms above show, the distributions of fantasy points differ considerably across player positions. Certain positions exhibit much wider spreads in fantasy point totals than others. For example, most FBs score fewer than 100 fantasy points and make up the majority of players with fewer than 50 fantasy points overall. In contrast, QBs are distributed across a much wider range of fantasy point totals, despite having a much smaller player population, and account for most players with 200 or more fantasy points. WRs and RBs, however, include many more players and exhibit a more gradual decline in player counts, from roughly 800 players with fewer than 50 fantasy points to fewer than 100 players with more than 300 fantasy points. These differences in the positional distributions may contribute to the improved performance of the *polynomial ridge regression* model compared with a simple *linear regression* model.