# UI lab results

Tool parts that I chose: Part 3, Robot Framework, and Part 4, Cypress

## Part 0
- Step 0.1, Chrome version: Google Chrome 158.0.8127.37 (Official Build) (arm64)
- Step 0.3, ChromeDriver version: ChromeDriver 158.0.8127.37 (b8c2be851f62f6a394ec57955d7f31335d7cb6d4-refs/branch-heads/8127@{#1692})
- Step 0.4, locator of Pizza, Please!: `button[name="pizza-please"]`
- Step 0.4, locator of Excluded tools: `select` (the only list on the page)
- Step 0.4, locator of Must be vegetarian: `#default-checkbox`
- Step 0.4, locator of Custom Pizza Name: `input#pizza-name` (`#pizza-name` alone also matches the heading of the answer)
- Step 0.5, pizza name, tool and calories of the run by hand: TODO, run QP-01 by hand in Chrome and fill in

| Step | Actual result | Pass or fail |
|------|---------------|--------------|
| 1    | TODO          |              |
| 2    | TODO          |              |
| 3    | TODO          |              |
| 4    | TODO          |              |
| 5    | TODO          |              |
| 6    | TODO          |              |
| 7    | TODO          |              |

## Part 3: Robot Framework
- robot --version: Robot Framework 7.5.1 (Python 3.15.1 on darwin)
- Step 3.2, failure message for the wrong title: `Title should have been 'Web Form' but was 'Web form'.` and `1 test, 0 passed, 1 failed`
- Step 3.8, run 1, result, elapsed time and pizza name: PASS, 1 test, 1 passed, 0 failed, elapsed 00:00:07.026, Lab Pizza 20261124-231746 (tool Scissors, 400 calories per slice)
- Step 3.8, run 2, result, elapsed time and pizza name: PASS, 1 test, 1 passed, 0 failed, elapsed 00:00:09.058, Lab Pizza 20261124-231755 (tool Pizza cutter, 600 calories per slice)

## Part 4: Cypress
- node --version, npm --version and npx cypress version: v24.22.1, 11.19.1, Cypress 16.3.0 (the sheet's 16.2.1 crashed with `* is not a valid URL` between specs on Chrome 158)
- Step 4.3, failure message for the wrong title: `Timed out retrying after 10000ms`, `+ expected - actual`, `-'Web form'`, `+'Web Form'`
- Step 4.6, tool and calories in the answer of POST /api/pizza: run 1, tool Pizza cutter and 325 calories per slice, the same as the page showed. Run 2, tool Scissors and 375 calories per slice
- Step 4.7, the line of the admin list that shows your pizza: `Lab Pizza 20261124-230217 (tool=Scissors, ingredients_number=6)`
- Step 4.8, run 1, result, duration and pizza name: All specs passed, 2 of 2 tests, 00:12 (QP-01 00:11), Lab Pizza 20261124-230140
- Step 4.8, run 2, result, duration and pizza name: All specs passed, 2 of 2 tests, 00:12 (QP-01 00:10), Lab Pizza 20261124-230217
- Step 4.9 (optional), browser and result: not done, Firefox is not installed

## Part B
1. The three number boxes of QuickPizza all have the id `floating_filled`, so `$$("#floating_filled").length` returns 3, and an id locator picks Max Calories per Slice only because it happens to come first. The label text Max Calories per Slice is unique, so the tests find the label and step to the input next to it, as `cy.contains('label', 'Max Calories per Slice').siblings('input')` does in Cypress. The page should give each box its own id, such as `max-calories`, `min-toppings` and `max-toppings`, and link each label to it with `for`. A unique id is short and fast, and it does not break when the label text is reworded or translated, so a tester can write `#max-calories` and stop depending on the layout around the box.

2. The check is a consistency oracle. It compares two outputs of the system with each other, the tool in the recommendation and the tool stored in the admin list, rather than with a value known in advance, because the tool is random. It would catch a fault where the server saves a different pizza than the one it sends to the page, for example when the handler of POST /api/pizza stores the tool of an earlier request. The runs also showed why the check searches the whole list. In run 2 the pizza was third, under two pizzas of other users, and in run 1 the list held two lines named Lab Pizza 20261124-230140, so a name with only the date and time is not unique among students, and a group number in the name, as Project 2 asks, avoids that.

3. A check could read the ingredients of the answer and fail if any of them is meat or fish, for example Prosciutto or Pepperoni. The page does not say which ingredients are vegetarian, so the test needs another source for that. The answer of POST /api/pizza already carries `"vegetarian": true`, so in Cypress the intercept from Step 4.6 can check `expect(response.body.vegetarian).to.be.true`. A stronger oracle would take the ingredient list from the QuickPizza source or ingredients API, where each ingredient is marked vegetarian or not, and check every ingredient of the pizza against it, because the flag only repeats the server's own claim.

4. Cypress waited by itself. Every `cy.get` and `should` retried for up to 10 seconds, so QP-01 needed no explicit wait, and `cy.wait('@pizza')` waited for the real request instead of a fixed time. Robot Framework needed an explicit wait keyword before each check of something that appears late, such as `Wait Until Element Is Visible css:h1` and `Wait Until Page Contains Element id:svelte-announcer`. Cypress was quicker to write and set up, while Robot Framework needed a virtual environment and a ChromeDriver that matches Chrome 158 exactly, but its keywords read like the written test case. QP-01 took 7 and 9 seconds in Robot Framework and 11 and 10 seconds in Cypress, so neither tool was clearly faster. The Cypress video and the three screenshots showed each run clearly, with the Command Log next to the page. The Robot log.html was the easier report to read after a run, because each of my keywords opens into the SeleniumLibrary steps inside it, with values such as `${calories} = 600` and the screenshots in place. That value in run 2 also showed that the boundary of 600 calories passes the check, as the test case intends.

5. I would choose Cypress for Project 2. First, it starts and drives Chrome itself, so it needed no ChromeDriver, which in this lab had to match Chrome 158 exactly although Chrome updates every four weeks. Second, its automatic retry removed every hand-written wait from QP-01, and `cy.intercept` let the test check the request and the answer behind the page, including the limit of 600 in the request body. The video of each spec is also ready-made evidence for the project. Its limits are one browser per test and the crash that I hit with Cypress 16.2.1, which updating to 16.3.0 fixed.

6. Part 3: the locator of the calories box lives only in the variable `${MAX CALORIES}` in `resources/quickpizza.resource`, which the keywords Turn On Advanced and Fill In The Pizza Request use. If QuickPizza changed the box, I would change that one line in the resource file. `pizza_scenario.robot` only calls keywords such as `Fill In The Pizza Request 600 Knife ${name}` and never names a locator, so the test file stays the same, and every test that uses the resource file is repaired at once.

7. Part 4: Cypress clears the cookies, local storage and session storage before each test. QuickPizza runs on several servers, a cookie keeps one browser on one server, and the admin list shows only the pizzas made on that server. The admin lists of run 1 and run 2 show completely different pizzas, which shows that the two runs landed on different servers. If steps 6 and 7 were a second test, that test would start without the cookie, could reach another server, and would not find the pizza from steps 1 to 5. It would also lose `pizzaName` and the alias `@tool`, because aliases are reset between tests.
