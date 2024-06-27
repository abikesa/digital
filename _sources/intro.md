# WebApp

This started as a fork of our [creatinine calculator](https://kidneycalculator.github.io). [Version 0.0](https://github.com/muzaale/webApp) then gave way to [version 1.1](https://abikesa.github.io/webApp/), with text as output. The next, [version 1.2](https://jhurepos.github.io/webApp.github.io/) introduced graphical output, but with no step-step function. It was [version 1.3](https://abikesa.github.io/quickdeploy/) that gave us a step by step function, but with only four-hardcoded datapoints per univariable clinical scenario. And today, we proudly announce [version 1.4](https://abikesa.github.io/edmonds/foreword/foreword.html) with the following innovations:
1. No hardcording of calculator inputs. The `script.js` imports the coefficient vector and base-case Kaplan-Meier curve from a remote server hosting `.csv` files.
2. Granular datapoints. So we now have a step-step function at each event among 80,000 clinical observations.
3. Embedding within text. The webApp is now presented within the context of mock educational literature, mimicking the ultimate purpose: patient education.

Check out the content pages bundled with this sample book to see more.

```{tableofcontents}
```
