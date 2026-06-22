## Analisi Statistiche sulle Serie Storiche e Modellizzazione della Varianza Condizionata

Il primo punto della nostra analisi è stato il test Augmented Dickey-Fuller per verificare la stazionarietà delle serie, fondamentale per evitare il fenomeno delle regressioni spurie e per garantire l'efficacia degli stimatori nei modelli successivi (come AR e VAR). I p-value registrati per i rendimenti di Linde (LIN), Tokyo Electron (TOELY) e Samsung Electronics (SSNLF) sono risultati inferiori a 0.05, il che porta a rifiutare l'ipotesi nulla di radice unitaria e a confermarne la stazionarietà.

In seguito, si è proceduto ad analizzare la normalità della distribuzione mediante il test di Jarque-Bera. Anche in questo caso, i risultati hanno portato al rifiuto dell'ipotesi nulla di distribuzione normale per tutte le serie. Questo risultato indica la presenza di asimmetria e di curtosi elevata nei dati storici. Ciò può essere spiegato dal fatto che, nel contesto dei mercati finanziari e specificamente dei semiconduttori, si verificano spesso gap di prezzo improvvisi legati a dinamiche geopolitiche, dazi doganali o innovazioni tecnologiche improvvise. Questa dinamica genera una distribuzione tipicamente leptocurtica, caratterizzata da code pesanti e da un picco molto pronunciato in corrispondenza del valore medio.

Al fine di modellare la media, tutte le serie sono state modellate mediante un processo autoregressivo di primo ordine, ovvero un AR(1). Questa scelta permette di catturare e filtrare un'eventuale autocorrelazione seriale nei rendimenti, garantendo che la successiva analisi dell'eteroschedasticità avvenga su residui puri da dinamiche di breve periodo. Per verificare la validità del modello AR(1), è stato effettuato il test di Ljung-Box sui residui standardizzati. Questo test è fondamentale per verificare se vi è autocorrelazione dei residui che andrebbe a rendere gli stimatori inefficienti e distorti. L'ipotesi nulla indica l'assenza di autocorrelazione seriale residua. I risultati ottenuti hanno validato il filtro utilizzato: i p-value risultano di 0.6589 per LIN, 0.7342 per TOELY e 0.5303 per SSNLF. Dato che tali valori superano ampiamente il livello di significatività del 5%, non vi sono le condizioni per rifiutare l'ipotesi nulla.

Successivamente, si è proceduto a testare la presenza di autoregressione eteroschedastica condizionata tramite il test ARCH sui residui. L'evidenza ha portato a rifiutare l'ipotesi nulla di omoschedasticità, confermando la presenza di volatility clustering, condizione necessaria affinché si possano stimare modelli della famiglia ARCH e GARCH. Sono state pertanto confrontate diverse specificazioni (ARCH(1), ARCH(2), GARCH(1,1), GARCH(1,2), GARCH(2,1), GARCH(2,2)). La selezione del modello ottimale è avvenuta minimizzando il Bayesian Information Criterion (BIC), che penalizza efficacemente la sovra-parametrizzazione.

| Struttura Condizionata | BIC LIN | BIC TOELY | BIC SSNLF |
| :--- | :--- | :--- | :--- |
| Modello GARCH(1,1) | 3540.02 | 4448.22 | 3936.34 |

Per tutti e tre i titoli, il modello GARCH(1,1) è risultato strettamente dominante. La diagnostica finale ha confermato l'efficacia della modellazione congiunta: riapplicando il test ARCH sui residui standardizzati del GARCH(1,1), non si rifiuta più l'ipotesi nulla di omoschedasticità. I p-value registrati, infatti, sono risultati pari a 0.9136 per LIN, 0.7219 per TOELY e 0.5492 per SSNLF, dimostrando definitivamente che l'eteroschedasticità iniziale è stata completamente assorbita dal modello.

---

### Previsioni di Volatilità sui Rendimenti

Di seguito si riportano le previsioni della volatilità attesa (deviazione standard condizionata, $\hat{\sigma}_{T+h}$) su un orizzonte di 3 settimane per ciascun titolo:

| Titolo | Settimana 1 ($\hat{\sigma}_{T+1}$) | Settimana 2 ($\hat{\sigma}_{T+2}$) | Settimana 3 ($\hat{\sigma}_{T+3}$) |
| :--- | :--- | :--- | :--- |
| **LIN** | 2.4927 | 2.5323 | 2.5682 |
| **TOELY** | 4.9834 | 5.0119 | 5.0368 |
| **SSNLF** | 3.7180 | 3.7134 | 3.7091 |

Dai dati si osserva che TOELY è il titolo intrinsecamente più volatile, seguito da SSNLF e LIN. Le proiezioni di LIN e TOELY mostrano una lieve traiettoria crescente, suggerendo un accumulo di incertezza, mentre SSNLF presenta una volatilità in lieve ma costante decrescita verso la media incondizionata.

---

## Il Modello VAR e Ordine di Cholesky

Per analizzare la trasmissione simultanea degli shock tra i tre titoli è stato stimato un Modello Autoregressivo Vettoriale (VAR) con un ritardo (p=1). Poiché la matrice di varianza-covarianza dei residui in forma ridotta ha evidenziato correlazioni incrociate non nulle, si è resa necessaria un'identificazione strutturale per isolare gli shock puri.

L'ordinamento di Cholesky scelto riflette in modo stringente la struttura della catena del valore della produzione di semiconduttori: **LIN $\rightarrow$ TOELY $\rightarrow$ SSNLF**.

* **LIN (Linde):** Opera a monte fornendo materiali chimici e gas industriali di base. Il suo shock è considerato esogeno e si propaga istantaneamente a tutta la catena.
* **TOELY (Tokyo Electron):** Fornitore di macchinari e attrezzature intermedie. Dipende dai materiali di base (LIN) e reagisce ai suoi shock, ma trasferisce la propria incertezza ai produttori finali.
* **SSNLF (Samsung Electronics):** Elettronica di consumo e fonderia a valle. Subisce gli shock di tutta la catena di fornitura, ma i suoi shock non si ripercuotono istantaneamente sui fornitori a monte.

---

### Funzioni di Risposta all'Impulso (IRF)

L'IRF ci permette di analizzare la direzione, la persistenza e il tempo di riassorbimento dell'effetto di uno shock su una determinata variabile. Applicando questo strumento al nostro modello, si osservano le seguenti dinamiche di trasmissione lungo la catena di fornitura:

<p align="center">
  <img width="723" height="443" alt="e0582d83-5504-42b9-a27c-2e04bcfb9c0c" src="https://github.com/user-attachments/assets/85f6ae94-0ef7-4437-b938-dc247b7e99eb" />
</p>


**1. Shock generati da Linde (LIN)**
* **Impatto su LIN stessa:** Uno shock LIN su LIN mostra un picco altissimo, prossimo a 3.0 alla settimana 0, subendo poi un ritracciamento negativo in area -0.22 alla settimana 1 per poi annullarsi. Il mercato azionario assorbe l'informazione in modo efficiente e rapidissimo.
* **Impatto su TOELY e SSNLF:** La trasmissione a valle presenta un forte impatto contemporaneo. TOELY registra un balzo esplosivo di oltre 2.0 al tempo 0 (azzerandosi quasi istantaneamente nella settimana 1), mentre SSNLF sale a circa 1.4 al tempo 0, per poi spegnersi linearmente. Questo dimostra come la chimica di base guidi prepotentemente il destino del resto della filiera.

**2. Shock generati da Tokyo Electron (TOELY)**
* **Impatto su TOELY stessa:** Lo shock di TOELY su TOELY mostra un incremento altissimo di circa 4.75 al tempo 0, correggendo immediatamente verso la neutralità nei periodi successivi.
* **Impatto su SSNLF:** Uno shock TOELY genera una forte risposta contemporanea a valle su SSNLF, con un impatto a circa 0.96 al periodo 0, per poi collassare a -0.033 alla settimana 2. Questo dimostra che il costruttore di macchinari detta incontestabilmente il passo tecnologico del fonditore finale.
* **Impatto su LIN:** Uno shock TOELY su LIN, per costruzione, presenta un effetto al tempo zero nullo. Nelle settimane 1 e 2 si notano solo variazioni irrisorie e fluttuanti (tra 0.09 e -0.014). L'ingegneria dei macchinari non possiede la leva per alterare permanentemente le dinamiche del fornitore a monte.

**3. Shock generati da Samsung Electronics (SSNLF)**
* **Impatto su SSNLF stessa:** L'incremento è poderoso, superiore a 3.0 al tempo 0, seguito da una variazione negativa di -0.29 nella settimana 1, per poi stabilizzarsi sullo zero.
* **Impatto su LIN e TOELY:** Al di là dell'impatto nullo al tempo 0 imposto dalla struttura del modello, la trasmissione a ritroso genera solo un rumore di breve durata. Alla settimana 1, lo shock su LIN scivola leggermente sotto zero (-0.06) per poi rimbalzare a 0.017, mentre su TOELY si denota un balzo asincrono a 0.327 seguito da fluttuazioni negative. Questo certifica che il rischio asimmetrico scorre dal fornitore all'acquirente, e non viceversa.

---

### Scomposizione della Varianza (FEVD)

La tabella seguente riporta la scomposizione della varianza dell'errore di previsione alla 16ª settimana:

| Variabile | Shock LIN (%) | Shock TOELY (%) | Shock SSNLF (%) |
| :--- | :--- | :--- | :--- |
| **Varianza LIN** | 99.87% | 0.09% | 0.04% |
| **Varianza TOELY** | 16.18% | 83.42% | 0.40% |
| **Varianza SSNLF** | 14.22% | 7.52% | 78.25% |

I risultati della FEVD dimostrano in modo empirico le dinamiche della supply chain: LIN è un titolo del tutto esogeno, con la varianza dettata al 99.87% da dinamiche proprie. TOELY mantiene una forte autonomia (83.42%), ma assorbe un notevole 16.18% di incertezza proveniente dai materiali base. Infine, SSNLF risulta essere l'anello più esposto: sebbene il 78.25% della volatilità sia idiosincratica, oltre il 21% del suo rischio di lungo periodo è "importato" dall'instabilità dei suoi fornitori (LIN e TOELY).

---

## Modelli GARCH sui Residui del VAR (Shock in forma ridotta)

Per l'approccio multivariato al rischio, sono stati estratti i residui in forma ridotta dal modello VAR (gli shock inattesi depurati dall'autocorrelazione della media). Anche su queste tre serie, il test ARCH ha rivelato forte eteroschedasticità. Applicando la medesima procedura di minimizzazione del BIC utilizzata nel primo step, il **GARCH(1,1)** si è confermato ancora una volta il modello ottimale per tutte le componenti strutturali.

| Shock | Settimana 1 ($\hat{\sigma}_{sh,1}$) | Settimana 2 ($\hat{\sigma}_{sh,2}$) | Settimana 3 ($\hat{\sigma}_{sh,3}$) |
| :--- | :--- | :--- | :--- |
| **Shock LIN** | 2.5161 | 2.5512 | 2.5831 |
| **Shock TOELY** | 5.0009 | 5.0245 | 5.0454 |
| **Shock SSNLF** | 3.7658 | 3.7605 | 3.7554 |

Le previsioni della volatilità sugli shock puri mostrano valori lievemente superiori rispetto alle stime fatte sui rendimenti grezzi del punto 1. Ciò accade perché la varianza isolata dal VAR quantifica l'incertezza delle "sorprese" di mercato, depurate da ogni trend prevedibile. 

---

## Confronto tra Modello VaR Univariato e Multivariato

L'analisi procede con la stima del Value at Risk (VaR), che quantifica la massima perdita potenziale su un orizzonte temporale di una settimana a un livello di confidenza del 95%. Il portafoglio di riferimento ha un valore di $300, suddiviso in parti uguali ($100) sui tre titoli. Sotto l'ipotesi di normalità della distribuzione, il calcolo impiega il quantile normale standard $z=1.6449$.

I due approcci si differenziano nella costruzione della matrice di varianza-covarianza del portafoglio:

1. **Approccio Univariato:** Utilizza le stime $\hat{\sigma}_{T+1}$ estratte dai modelli AR-GARCH sui rendimenti (Punto 1) combinate con la matrice di *correlazione storica incondizionata* dei rendimenti.
2. **Approccio Multivariato:** Utilizza le stime $\hat{\sigma}_{sh,1}$ ottenute dai GARCH sugli shock (Punto 3) combinate con la *matrice di covarianza implicita dei residui VAR*, catturando la correlazione pura degli shock inattesi contemporanei.

| Modello | VaR (Dollari USD) | VaR (Percentuale %) |
| :--- | :--- | :--- |
| **VaR Univariato (AR-GARCH)** | $ 14.34 | 4.78% |
| **VaR Multivariato (VAR-GARCH)** | $ 14.50 | 4.83% |

Sebbene la differenza quantitativa non sia drammatica, il VaR calcolato mediante l'approccio multivariato risulta sistematicamente superiore ($14.50 contro $14.34). La differenza tra i due metodi deriva da due scelte fondamentali:

1. Il modello univariato calcola il rischio utilizzando i rendimenti storici "grezzi", che mescolano le normali e prevedibili oscillazioni quotidiane con i veri shock imprevedibili. Questa metodologia rischia di sottostimare l'esposizione al rischio nei momenti di apparente stabilità autoregressiva. Il modello multivariato, al contrario, funziona come un filtro che pulisce i dati dalle tendenze legate al passato (le dinamiche autoregressive attese) per estrarre esclusivamente le turbolenze "a sorpresa" che il mercato non poteva in alcun modo anticipare. Concentrandosi unicamente su questi movimenti improvvisi (i puri residui del VAR estratti al Punto 3), il modello fa emergere volatilità marginali lievemente superiori e più realistiche, portando in emersione rischi nascosti che nell'approccio univariato finivano per essere diluiti dalle fluttuazioni ordinarie.
2. Calcolare le correlazioni sulla base dei soli shock, anziché sull'intera serie dei rendimenti, permette di isolare il comportamento dei titoli in condizioni di stress. Nei periodi di calma o di ordinaria stabilità, infatti, l'approccio univariato spalma le correlazioni sull'intero periodo storico, diluendo i momenti di crisi. Al contrario, la matrice di correlazione estratta dai residui del VAR cattura i co-movimenti "istantanei": quando il mercato subisce uno shock macro o strutturale sulla catena del valore (come descritto nel punto 2 dalle IRF), i titoli tendono a subire contrazioni sincrone e a muoversi in modo coeso. Questa dinamica aumenta la correlazione percepita durante gli eventi avversi, indebolendo il beneficio della diversificazione settoriale ed evitando che i giorni di stabilità vadano a mitigare la reale percezione del rischio, spingendo così la stima multivariata su livelli più elevati e conservativi.




## Time Series Statistical Analysis and Conditional Variance Modeling

The first step of our analysis involved the Augmented Dickey-Fuller test to check the stationarity of the series, which is crucial to avoid spurious regressions and ensure the efficiency of estimators in subsequent models (such as AR and VAR). The p-values recorded for the returns of Linde (LIN), Tokyo Electron (TOELY), and Samsung Electronics (SSNLF) were all below 0.05, leading to the rejection of the null hypothesis of a unit root and confirming their stationarity.

Subsequently, the normality of the distribution was analyzed using the Jarque-Bera test. Here too, the results led to the rejection of the null hypothesis of a normal distribution for all series. This indicates the presence of asymmetry and high kurtosis in the historical data. This can be explained by the fact that in financial markets, particularly in the semiconductor sector, sudden price gaps often occur due to geopolitical dynamics, tariffs, or sudden technological breakthroughs. This dynamic generates a typically leptokurtic distribution, characterized by heavy tails and a very pronounced peak at the mean value.

To model the mean, all series were fitted with a first-order autoregressive process, an AR(1). This choice allows us to capture and filter out any serial autocorrelation in the returns, ensuring that the subsequent heteroskedasticity analysis is performed on pure residuals free from short-term dynamics. To verify the validity of the AR(1) model, a Ljung-Box test was performed on the standardized residuals. This test is essential to check for residual autocorrelation, which would make the estimators inefficient and biased. The null hypothesis implies no residual serial autocorrelation. The results validated the filter used: the p-values were 0.6589 for LIN, 0.7342 for TOELY, and 0.5303 for SSNLF. Since these values are well above the 5% significance level, there are no grounds to reject the null hypothesis.

Next, we tested for the presence of autoregressive conditional heteroskedasticity using the ARCH test on the residuals. The evidence led to the rejection of the null hypothesis of homoscedasticity, confirming the presence of volatility clustering, a necessary condition to estimate ARCH and GARCH family models. Therefore, several specifications were compared (ARCH(1), ARCH(2), GARCH(1,1), GARCH(1,2), GARCH(2,1), GARCH(2,2)). The optimal model was selected by minimizing the Bayesian Information Criterion (BIC), which effectively penalizes over-parameterization.

| Conditional Structure | BIC LIN | BIC TOELY | BIC SSNLF |
| :--- | :--- | :--- | :--- |
| GARCH(1,1) Model | 3540.02 | 4448.22 | 3936.34 |

For all three assets, the GARCH(1,1) model was strictly dominant. Final diagnostics confirmed the effectiveness of the joint modeling: re-applying the ARCH test on the standardized residuals of the GARCH(1,1) model, the null hypothesis of homoscedasticity is no longer rejected. The recorded p-values were 0.9136 for LIN, 0.7219 for TOELY, and 0.5492 for SSNLF, definitively proving that the initial heteroskedasticity was completely absorbed by the model.

---

### Return Volatility Forecasts

Below are the expected volatility forecasts (conditional standard deviation, $\hat{\sigma}_{T+h}$) over a 3-week horizon for each asset:

| Asset | Week 1 ($\hat{\sigma}_{T+1}$) | Week 2 ($\hat{\sigma}_{T+2}$) | Week 3 ($\hat{\sigma}_{T+3}$) |
| :--- | :--- | :--- | :--- |
| **LIN** | 2.4927 | 2.5323 | 2.5682 |
| **TOELY** | 4.9834 | 5.0119 | 5.0368 |
| **SSNLF** | 3.7180 | 3.7134 | 3.7091 |

The data shows that TOELY is inherently the most volatile asset, followed by SSNLF and LIN. The projections for LIN and TOELY show a slight upward trajectory, suggesting an accumulation of uncertainty, while SSNLF displays a slight but steady downward volatility path toward its unconditional mean.

---

## The VAR Model and Cholesky Ordering

To analyze the simultaneous transmission of shocks among the three assets, a Vector Autoregressive (VAR) model with one lag (p=1) was estimated. Because the variance-covariance matrix of the reduced-form residuals showed non-zero cross-correlations, a structural identification was necessary to isolate pure shocks.

The chosen Cholesky ordering strictly reflects the structure of the semiconductor production value chain: **LIN $\rightarrow$ TOELY $\rightarrow$ SSNLF**.

* **LIN (Linde):** Operates upstream by supplying basic chemical materials and industrial gases. Its shock is considered exogenous and propagates instantly throughout the chain.
* **TOELY (Tokyo Electron):** Midstream supplier of machinery and equipment. It depends on basic materials (LIN) and reacts to its shocks, but transfers its own uncertainty to downstream final producers.
* **SSNLF (Samsung Electronics):** Downstream consumer electronics and foundry. It absorbs shocks from the entire supply chain, but its shocks do not instantly affect upstream suppliers.

---

### Impulse Response Functions (IRF)

The IRF allows us to analyze the direction, persistence, and absorption time of a shock's effect on a given variable. Applying this tool to our model, the following transmission dynamics along the supply chain are observed:

<p align="center">
  <img width="723" height="443" alt="e0582d83-5504-42b9-a27c-2e04bcfb9c0c" src="https://github.com/user-attachments/assets/85f6ae94-0ef7-4437-b938-dc247b7e99eb" />
</p>


**1. Shocks generated by Linde (LIN)**
* **Impact on LIN itself:** A LIN shock on LIN shows a very high peak, close to 3.0 at week 0, then undergoes a negative retracement around -0.22 at week 1 before dying out. The stock market absorbs the information very efficiently and rapidly.
* **Impact on TOELY and SSNLF:** The downstream transmission has a strong contemporaneous impact. TOELY records an explosive jump of over 2.0 at time 0 (zeroing out almost instantly in week 1), while SSNLF rises to about 1.4 at time 0, before fading linearly. This demonstrates how base chemistry strongly dictates the fate of the rest of the supply chain.

**2. Shocks generated by Tokyo Electron (TOELY)**
* **Impact on TOELY itself:** A TOELY shock on TOELY shows a massive increase of about 4.75 at time 0, immediately correcting towards neutrality in subsequent periods.
* **Impact on SSNLF:** A TOELY shock generates a strong downstream contemporaneous response on SSNLF, with an impact of about 0.96 at period 0, before collapsing to -0.033 in week 2. This shows that the machinery builder unquestionably sets the technological pace for the final foundry.
* **Impact on LIN:** By construction, a TOELY shock on LIN presents a zero effect at time zero. In weeks 1 and 2, only negligible and fluctuating variations are observed (between 0.09 and -0.014). Machinery engineering lacks the leverage to permanently alter the dynamics of the upstream supplier.

**3. Shocks generated by Samsung Electronics (SSNLF)**
* **Impact on SSNLF itself:** The increase is powerful, exceeding 3.0 at time 0, followed by a negative variation of -0.29 in week 1, before stabilizing at zero.
* **Impact on LIN and TOELY:** Beyond the zero impact at time 0 imposed by the model's structure, the backward transmission generates only short-lived noise. In week 1, the shock on LIN slides slightly below zero (-0.06) and then rebounds to 0.017, while TOELY sees an asynchronous jump to 0.327 followed by negative fluctuations. This certifies that asymmetric risk flows from supplier to buyer, and not vice versa.

---

### Forecast Error Variance Decomposition (FEVD)

The following table reports the Forecast Error Variance Decomposition at the 16th week:

| Variable | LIN Shock (%) | TOELY Shock (%) | SSNLF Shock (%) |
| :--- | :--- | :--- | :--- |
| **LIN Variance** | 99.87% | 0.09% | 0.04% |
| **TOELY Variance** | 16.18% | 83.42% | 0.40% |
| **SSNLF Variance** | 14.22% | 7.52% | 78.25% |

The FEVD results empirically demonstrate the dynamics of the supply chain: LIN is a completely exogenous stock, with its variance dictated 99.87% by its own dynamics. TOELY maintains strong autonomy (83.42%), but absorbs a notable 16.18% of uncertainty from base materials. Finally, SSNLF proves to be the most exposed link: although 78.25% of its volatility is idiosyncratic, over 21% of its long-term risk is "imported" from the instability of its suppliers (LIN and TOELY).

---

## GARCH Models on VAR Residuals (Reduced-form Shocks)

For the multivariate approach to risk, the reduced-form residuals were extracted from the VAR model (the unexpected shocks stripped of mean autocorrelation). On these three series as well, the ARCH test revealed strong heteroskedasticity. Applying the same BIC minimization procedure used in the first step, the **GARCH(1,1)** was once again confirmed as the optimal model for all structural components.

| Shock | Week 1 ($\hat{\sigma}_{sh,1}$) | Week 2 ($\hat{\sigma}_{sh,2}$) | Week 3 ($\hat{\sigma}_{sh,3}$) |
| :--- | :--- | :--- | :--- |
| **LIN Shock** | 2.5161 | 2.5512 | 2.5831 |
| **TOELY Shock** | 5.0009 | 5.0245 | 5.0454 |
| **SSNLF Shock** | 3.7658 | 3.7605 | 3.7554 |

The volatility forecasts on pure shocks show slightly higher values compared to the estimates made on the raw returns in step 1. This happens because the variance isolated from the VAR quantifies the uncertainty of market "surprises," purged of any predictable trend.

---

## Comparison Between Univariate and Multivariate VaR Models

The analysis proceeds with the estimation of the Value at Risk (VaR), which quantifies the maximum potential loss over a one-week time horizon at a 95% confidence level. The reference portfolio has a value of $300, divided equally ($100) among the three stocks. Assuming a normal distribution, the calculation employs the standard normal quantile $z=1.6449$.

The two approaches differ in the construction of the portfolio's variance-covariance matrix:

1. **Univariate Approach:** Uses the $\hat{\sigma}_{T+1}$ estimates extracted from the AR-GARCH models on returns (Step 1) combined with the *unconditional historical correlation* matrix of the returns.
2. **Multivariate Approach:** Uses the $\hat{\sigma}_{sh,1}$ estimates obtained from the GARCH on shocks (Step 3) combined with the *implied covariance matrix of the VAR residuals*, capturing the pure correlation of simultaneous unexpected shocks.

| Model | VaR (USD Dollars) | VaR (Percentage %) |
| :--- | :--- | :--- |
| **Univariate VaR (AR-GARCH)** | $ 14.34 | 4.78% |
| **Multivariate VaR (VAR-GARCH)** | $ 14.50 | 4.83% |

Although the quantitative difference is not drastic, the VaR calculated using the multivariate approach is systematically higher ($14.50 vs. $14.34). The difference between the two methods stems from two fundamental choices:

1. The univariate model calculates risk using "raw" historical returns, which mix normal, predictable daily fluctuations with true unpredictable shocks. This methodology risks underestimating risk exposure during times of apparent autoregressive stability. The multivariate model, conversely, acts as a filter that cleanses the data of past trends (expected autoregressive dynamics) to extract only the "surprise" turbulence that the market could not anticipate in any way. By focusing solely on these sudden movements (the pure VAR residuals extracted in Step 3), the model surfaces slightly higher and more realistic marginal volatilities, bringing to light hidden risks that would end up being diluted by ordinary fluctuations in the univariate approach.
2. Calculating correlations based solely on shocks, rather than on the entire series of returns, allows us to isolate the behavior of stocks under stress conditions. In periods of calm or ordinary stability, the univariate approach spreads correlations over the entire historical period, diluting moments of crisis. In contrast, the correlation matrix extracted from VAR residuals captures "instantaneous" co-movements: when the market suffers a macro or structural shock on the value chain (as described by the IRFs in Step 2), the stocks tend to undergo synchronous contractions and move cohesively. This dynamic increases the perceived correlation during adverse events, weakening the benefit of sector diversification and preventing days of stability from mitigating the true perception of risk, thereby pushing the multivariate estimate to higher and more conservative levels.
