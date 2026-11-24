# ui-lab

Automated UI testing lab for ITCS386 Software Verification and Validation.
The tests automate test case QP-01 on QuickPizza and fill in Selenium's web form.

Tool parts that I chose: **Part 3, Robot Framework** and **Part 4, Cypress**.

## robot/

Robot Framework tests with SeleniumLibrary. `first.robot` and `web_form.robot` test the
web form, and `pizza_scenario.robot` runs QP-01 with the keywords in
`resources/quickpizza.resource`. The folder `results/` holds the log, report and
screenshots of two full runs.

Run it from the `robot` folder, with ChromeDriver set in the `CHROMEDRIVER` variable:

```
python3 -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt
robot -d results/run1 -v BROWSER:headlesschrome pizza_scenario.robot
```

## cypress/

Cypress tests. `cypress/e2e/web-form.cy.js` tests the web form, and
`cypress/e2e/pizza-scenario.cy.js` runs QP-01, with the admin login as the custom command
`adminLogin` in `cypress/support/commands.js`. The folders `cypress/videos/` and
`cypress/screenshots/` hold the evidence of the full runs. `cypress/screenshots/run1/`
keeps the pictures of the first run, and the others are from the second run.

Run it from the `cypress` folder:

```
npm install
npx cypress run --browser chrome
```

The project uses Cypress 16.3.0 instead of the 16.2.1 in the lab sheet. With Chrome 158,
Cypress 16.2.1 crashed with `* is not a valid URL` when it moved from the first spec to the
second, and 16.3.0 runs both specs without the error.
