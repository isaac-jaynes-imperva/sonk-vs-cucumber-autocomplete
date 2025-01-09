# Cucumber Full Language Support
VSCode Cucumber (Gherkin) Language Support + Format + Steps/PageObjects Autocomplete

## This extension adds rich language support for the Cucumber (Gherkin) language to VS Code, including:
* Syntax highlight
* Basic Snippets support
* Auto-parsing of feature steps from paths, provided in settings.json
* Autocompletion of steps
* Ontype validation for all the steps
* Definitions support for all the steps parts
* Document format support, including tables formatting
* Supporting of many spoken languages
* Gherkin page objects native support
* Multiple programming languages, JS, TS, Ruby, Kotlin etc.

## Important extension goals are improving of steps suggestions list and minimization of user edits after step inserting:
* Sort steps suggestions by their using count
* Option to filter steps completions depending on words used for their defining
* Option to automatically change all the steps parts, that require some user action, by snippets
* Option to show several different completion variants for steps with 'or' RegEx parts (like `(a|b)`)

![](https://raw.githubusercontent.com/alexkrechik/VSCucumberAutoComplete/master/gclient/img/vscode.gif)
## How to use:
1. Open your app in VS Code
2. Install `sonkcucumberautocomplete` extension
3. In the opened app root create (if absent) .vscode folder with settings.json file or just run ```mkdir .vscode && touch .vscode/settings.json```
4. Add all the needed settings to the settings.json file
5. Reload app to apply all the extension changes
6. To get autocomplete working, `strings` var of `editor.quickSuggestions` setting should be set to true (because by default `string` suggestions will not appear)

## Settings:

### Basic settings example:
```javascript
{
    "sonk-cucumber-autocomplete.steps": [
        "test/features/step_definitions/*.js",
        "node_modules/qa-lib/src/step_definitions/*.js"
    ],
    "sonk-cucumber-autocomplete.strictGherkinCompletion": true
}
```

### All the settings description:
**`sonk-cucumber-autocomplete.steps`** - Glob-style path or array of glob-style paths to the gherkin steps files.
All the files, that match path provided, will be handled by the extension. So, ideally, this path should be as strict as possible (ex. `test/features/step_definitions/*.steps.js` is better then `test/**/*.steps.js` and much better then `**/*.steps.js`)
The Node will watch steps files for change and will automatically update steps in them.
All the paths are relative to the app root.

**`sonk-cucumber-autocomplete.syncfeatures`** - Will get steps using count from the glob-style path.
Same with the `steps` setting - this path should be as strict as possible.

**`sonk-cucumber-autocomplete.strictGherkinCompletion`** - Strict comparing of declaration function and gherkin word.
For ex. if step definition is `When(/I do something/)` - in case of `strictGherkinCompletion` is `true` - after typing `Given I` this step will not be shown in the suggestion list.
In case of some non-gherkin steps definition usage (ex. `new Step('I do something')`) `strictGherkinCompletion` should be set to `false` - no steps suggestions will be shown otherwise.

**`sonk-cucumber-autocomplete.strictGherkinValidation`** - Compare step body and gherkin word during steps validation.
Sometimes, it's useful to suggest only steps, that strictly equals by gherkin word, but show no error in case if gherkin word is using inproperly, so this option was separated from the `strictGherkinCompletion`.

**`sonk-cucumber-autocomplete.smartSnippets`** - Extension will try to change all the steps parts, that requires some user input (ex. .*, ([a-z]+), \\w{1,3}) to snippets.
This option could speed up new steps adding up to several times. Try it ;)

**`sonk-cucumber-autocomplete.stepsInvariants`** - Show all the 'or' steps parts as separate suggestions (ex. show `I use a` and `I use b` steps suggestions for the `Given(/I use (a|b)/)` step. It also could help to speed up the new steps adding.

**`sonk-cucumber-autocomplete.customParameters`** - Change some steps RegEx parts depending on array of 'parameter' - 'value' key pairs. Parameter could be string or RegEx object.
This setting will be applied before the steps getting.
For ex. to get step from the py expression `@given(u'I do something')` we could use the next parameters:
```
"sonk-cucumber-autocomplete.customParameters": [
        {
            "parameter":"(u'",
            "value":"('"
        }
    ],
```
After this, the current expression will be handled as `@given('I do something')`, so the extension would be able to get `'I do something'` step. 

**`sonk-cucumber-autocomplete.pages`** - Object, which consists of 'page name' => 'page object file path' pairs
It is allowing to handle some very specific case of page objects usage in the gherkin steps.

**`sonk-cucumber-autocomplete.skipDocStringsFormat`** - Skip format of strings, that placed between ''' or \"\"\".

**`sonk-cucumber-autocomplete.formatConfOverride`** - Override some formatting via format conf strings = {[key: String]: num | 'relative' | 'relativeUp' }, where key - beggining of the string, num - numeric value of indents, 'relative' (same indent value as the next line), or 'relativeUp' (same as the previous line).
Example:
```
"sonk-cucumber-autocomplete.formatConfOverride": {
        "And": 3,
        "But": "relative",
    },
```
Also, some new words (in the case of non-English languages using) could be added. Example:
```
"sonk-cucumber-autocomplete.formatConfOverride": {
        "Característica": 3,
        "Cuando": "relative",
    },
```
Default format conf is:
```
{
    'Ability': 0,
    'Business Need': 0,
    'Feature:': 0,
    'Scenario:': 1,
    'Background:': 1,
    'Scenario Outline:': 1,
    'Examples:': 2,
    'Given': 2,
    'When': 2,
    'Then': 2,
    'And': 2,
    'But': 2,
    '*': 2,
    '|': 3,
    '"""': 3,
    '#': 'relative',
    '@': 'relative',
};
```


**`sonk-cucumber-autocomplete.onTypeFormat`** - Enable ontype formattings (activating after pressing on space, @ and : keys)"

**`sonk-cucumber-autocomplete.gherkinDefinitionPart`** - Provide step definition name part of regex(ex. '@(given|when|then|step)\\(' in case of python-like steps.
All the 'definition' words (usually they are gherkin words, but some other words also could be used) should be placed into the braces.

**`sonk-cucumber-autocomplete.stepRegExSymbol`** - Provide step regex symbol. Ex. it would be \"'\" for When('I do something') definition
By default, all the `' ' "` symbols will be used do define start and the end of the regex. But, sometimes, we needs to use some other symbol (ex. `\\`) or we should exclude some default symbol (ex. use `'` only).

**`sonk-cucumber-autocomplete.pureTextSteps`** - Some frameworks using gherkin steps as a text with just support for the cucumber expression instead of RegExp. This differs from the default extension behaviour, example:
`When('I give 5$ and * items')` step would be handled as `/I give 5$ and * items/` RegExp without this option enabled and as `/^I give 5\$ and \* items$/` RegExp with it (`^` and `$` symbols were added to the reg ex and also all the special regex symbols were handled as regular text symbols).

### Using all the setting available example:
```javascript
{
    "sonk-cucumber-autocomplete.steps": [
        "test/features/step_definitions/*.js",
        "node_modules/qa-lib/src/step_definitions/*.js"
    ],
    "sonk-cucumber-autocomplete.syncfeatures": "test/features/*feature",
    "sonk-cucumber-autocomplete.strictGherkinCompletion": true,
    "sonk-cucumber-autocomplete.strictGherkinValidation": true,
    "sonk-cucumber-autocomplete.smartSnippets": true,
    "sonk-cucumber-autocomplete.stepsInvariants": true,
    "sonk-cucumber-autocomplete.customParameters": [
        {
            "parameter":"{ab}",
            "value":"(a|b)"
        },
        {
            "parameter":/\{a.*\}/,
            "value":"a"
        },
    ],
    "sonk-cucumber-autocomplete.pages": {
        "users": "test/features/page_objects/users.storage.js",
        "pathes": "test/features/page_objects/pathes.storage.js",
        "main": "test/features/support/page_objects/main.page.js"
    },
    "sonk-cucumber-autocomplete.skipDocStringsFormat": true,
    "sonk-cucumber-autocomplete.formatConfOverride": {
        "And": 3,
        "But": "relative",
    },
    "sonk-cucumber-autocomplete.onTypeFormat": true,
    "editor.quickSuggestions": {
        "comments": false,
        "strings": true,
        "other": true
    },
    "sonk-cucumber-autocomplete.gherkinDefinitionPart": "(Given|When|Then)\\(",
    "sonk-cucumber-autocomplete.stepRegExSymbol": "'",
    "sonk-cucumber-autocomplete.pureTextSteps": true
}
```
#### Issues
Feel free to create app issues on [GitHub](https://github.com/isaac-jaynes-imperva/sonk-vs-cucumber-autocomplete/issues)

#### Thank you
If this plugin was helpful for you, you could give it a ★ Star on [GitHub](https://github.com/isaac-jaynes-imperva/sonk-vs-cucumber-autocomplete)
