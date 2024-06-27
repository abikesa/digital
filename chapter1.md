# Versions

Of the key files: `risk.html` and `script.js`

## Risk

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mortality Risk Calculator</title>
</head>
<body>
    <div>
        <label for="scenario-dropdown">Select Scenario:</label>
        <select id="scenario-dropdown">
            <option value="donor">Donor</option>
            <option value="nondonor">Nondonor</option>
            <option value="genpop">Genpop</option>
            <option value="fair">Fair</option>
            <option value="poor">Poor</option>
        </select>
        <button id="calculate-risk-button" disabled>Calculate Mortality Risk</button>
    </div>
    <div style="width: 80vw; margin: 0 auto;">
        <canvas id="mortality-risk-graph" width="100%" height="1000"></canvas>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="assets/js/script.js"></script>
</body>
</html>
```

## Script

```js
'use strict';

var scenarioVector = [1, 0, 0]; // Default to excellent self-rated health scenario
let beta = [];
let s0 = [];
let timePoints = [];

async function fetchCSV(url) {
    const response = await fetch(url);
    const text = await response.text();
    return text.split('\n').map(row => row.trim()).filter(row => row);
}

async function loadData() {
    try {
        // Fetch coefficients
        const coefficientsData = await fetchCSV('https://abikesa.github.io/flow/_downloads/b57ad99810799d0be5a9e18f54115561/b.csv');
        const [header, ...rows] = coefficientsData;
        beta = rows[0].split(',').map(Number);
        console.log('Coefficients loaded:', beta);

        // Fetch survival data
        const survivalData = await fetchCSV('https://abikesa.github.io/flow/_downloads/9c26f2afd014707dc60aefc8facbf60d/s0.csv');
        const [survivalHeader, ...survivalRows] = survivalData;
        timePoints = [];
        s0 = [];
        survivalRows.forEach(row => {
            const [time, survival] = row.split(',').map(Number);
            timePoints.push(time);
            s0.push(survival);
        });
        console.log('Survival data loaded:', {timePoints, s0});

        // Enable the calculate button after data is loaded
        document.getElementById('calculate-risk-button').disabled = false;
    } catch (error) {
        console.error('Error loading data:', error);
        alert('Error loading data. Please check the console for details.');
    }
}

function selectScenario(scenario) {
    switch (scenario) {
        case 'donor':
            scenarioVector = [1, 0, 0];
            break;
        case 'nondonor':
            scenarioVector = [0, 1, 0];
            break;
        case 'genpop':
            scenarioVector = [0, 0, 1];
            break;
        case 'fair':
            scenarioVector = [0, 0, 0];
            break;
        case 'poor':
            scenarioVector = [0, 0, 0];
            break;
        default:
            scenarioVector = [0, 0, 1]; // Set default to 'good'
    }
    calculateMortalityRisk(scenario);
}

function calculateMortalityRisk(scenario) {
    if (beta.length === 0 || s0.length === 0 || timePoints.length === 0) {
        alert('Data is not yet loaded. Please wait.');
        return;
    }

    const logHR = beta.reduce((acc, curr, index) => acc + (curr * scenarioVector[index]), 0);
    const f0 = s0.map(s => (1 - s) * 100);
    const f1 = f0.map((f, index) => f * Math.exp(logHR));

    const colorSchemes = {
        'donor': 'rgba(0, 191, 255, 1)',
        'nondonor': 'rgba(255, 0, 255, 1)',
        'genpop': 'rgba(106, 168, 79, 1)',
        'fair': 'rgba(255, 218, 185, 1)',
        'poor': 'rgba(128, 0, 128, 1)'
    };

    const riskResults = timePoints.map((time, index) => `Risk at ${time.toFixed(2)} years: ${f1[index].toFixed(2)}%`);

    if (window.mortalityChart) {
        window.mortalityChart.destroy();
    }

    const ctx = document.getElementById('mortality-risk-graph').getContext('2d');
    window.mortalityChart = new Chart(ctx, {
        type: 'line',
        data: {
            labels: timePoints.map(t => t.toFixed(2)),
            datasets: [{
                label: 'Mortality Risk',
                data: f1,
                stepped: true,
                borderColor: colorSchemes[scenario],
                backgroundColor: colorSchemes[scenario].replace('1)', '0.2)'),
                borderWidth: 3
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                x: {
                    title: {
                        display: true,
                        text: 'Timepoints (years)'
                    }
                },
                y: {
                    title: {
                        display: true,
                        text: 'Mortality Risk (%)'
                    },
                    suggestedMin: 0,
                    suggestedMax: 80,
                    stepSize: 20
                }
            }
        }
    });

    document.getElementById("mortality-risk-results").innerText = riskResults.join('\n');
}

// Load data when the page loads
window.addEventListener('load', loadData);

// Attach event listener to the dropdown to update the scenarioVector
document.getElementById("scenario-dropdown").addEventListener("change", function() {
    selectScenario(this.value);
});

// Attach event listener to the calculate button
document.getElementById("calculate-risk-button").addEventListener("click", function() {
    const scenario = document.getElementById("scenario-dropdown").value;
    calculateMortalityRisk(scenario);
});
```

```{note}
Thu Jun 27 12:11 PM
```

It will be rendered in a special box when you build your book.

Here is an inline directive to refer to a document: {doc}`markdown-notebooks`.

## Shell

```sh
# Automated webApp embed on 06/27
read -p "Enter your GitHub username: " GITHUB_USERNAME
read -p "Enter your GitHub repository name: " REPO_NAME
read -p "Enter your email address: " EMAIL_ADDRESS
read -p "Enter your root directory (e.g., ~/Dropbox/1f.ἡἔρις,κ/1.ontology): " ROOT_DIR
read -p "Enter the name of the subdirectory to be built within the root directory: " SUBDIR_NAME
read -p "Enter your commit statement: " COMMIT_THIS

# Build the book with Jupyter Book
git config --local user.name "$GITHUB_USERNAME"
git config --local user.email "$EMAIL_ADDRESS"

cd "$(eval echo $ROOT_DIR)"

rm -rf $SUBDIR_NAME/_build; cuts runtimes by 90%+;
rm -rf $SUBDIR_NAME/_build
jb build $SUBDIR_NAME
mkdir -p _build/html/assets && mkdir -p _build/html/assets/js && mkdir -p _build/html/assets/css
cp risk.html _build/html/risk.html && cp assets/js/script.js _build/html/assets/js/script.js && cp assets/css/style.css _build/html/assets/css/style.css
rm -rf $REPO_NAME

if [ -d "$REPO_NAME" ]; then
  echo "Directory $REPO_NAME already exists. Choose another directory or delete the existing one."
  exit 1
fi

# Cloning
git clone "https://github.com/$GITHUB_USERNAME/$REPO_NAME.git"
if [ ! -d "$REPO_NAME" ]; then
  echo "Failed to clone the repository. Check your GitHub username, repository name, and permissions."
  exit 1
fi

# Copy files from subdirectory to the current repository directory; restored $REPO_NAME!!!
cp -r $SUBDIR_NAME/* $REPO_NAME
cd $REPO_NAME

git add ./*
git commit -m "$COMMIT_THIS"
git remote -v

git remote set-url origin "https://github.com/$GITHUB_USERNAME/$REPO_NAME.git"
git config --local user.name "$GITHUB_USERNAME"
git config --local user.email "$EMAIL_ADDRESS"

# Checkout the main branch
git checkout main
if [ $? -ne 0 ]; then
  echo "Failed to checkout the main branch. Make sure it exists in the repository."
  exit 1
fi

# Pushing changes
# git config pull.rebase true
# git pull
git push -u origin main
if [ $? -ne 0 ]; then
  echo "Failed to push to the repository. Check your credentials and GitHub permissions."
  exit 1
fi

ghp-import -n -p -f _build/html
cd ..
rm -rf $REPO_NAME
echo "Jupyter Book content updated and pushed to $GITHUB_USERNAME/$REPO_NAME repository!"

```

## Citations

You can also cite references that are stored in a `bibtex` file. For example,
the following syntax: `` {cite}`holdgraf_evidence_2014` `` will render like
this: {cite}`holdgraf_evidence_2014`.

Moreover, you can insert a bibliography into your page with this syntax:
The `{bibliography}` directive must be used for all the `{cite}` roles to
render properly.
For example, if the references for your book are stored in `references.bib`,
then the bibliography is inserted with:

```{bibliography}
```

## Learn more

This is just a simple starter to get you started.
You can learn a lot more at [jupyterbook.org](https://jupyterbook.org).
