# SSML Split

Splits SSML strings into batches AWS Polly, Azure and Google's Text to Speech API can consume.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

## Features

*  Splits your large SSML into batches AWS Polly, Azure and Google's Text to Speech API can consume.
*  Makes sure you stay below the API character limitations by configuring a `hardLimit`.
*  Creates the least possible batch size to limit your requests to the Text to Speech API's.
*  Will split text at the nearest `.`, `,`, `;` or space. Can be configured.
*  Sanitizes your SSML by removing new lines, excessive white spaces, and empty tags, resulting in less characters used.
*  Uses TypeScript so you can enjoy the type safety and documentation that comes with it.

Based on [ssml-split](https://github.com/jvandenaardweg/ssml-split) by [@jvandenaardweg ](https://github.com/jvandenaardweg) and
[polly-text-split](https://github.com/oleglegun/polly-text-split) by [@oleglegun](https://github.com/oleglegun)

## Documentation


## Installation
Install the package with:

```sh
pip3 install ...
```

## Usage

Import the package and set the options. Use the `.split()` method to split your SSML string. You can tweak the `soft_limit` to see what works for you. I suggest you keep the `hard_limit` at the limitation limit of the respective API:

```python
import SSMLSplit from 'ssml-split';

batches = SSMLSplit(soft_limit=2000, hard_limit=3000, synthesizer='aws', break_paragraphs_above_hard_limit=True).split(ssml='text')
```


| Option              | Type | Default                       | Description                                                                           |
| ------------------- | ---- | ------------------------- | ------------------------------------------------------------------------------------- |
| `synthesizer` | `string` | `aws`  | Set to which synthesizer you are using. Useful for when you use `breakParagraphsAboveHardLimit`. It allows the library to determine the correct break length, as that differs per synthesizer service. |
| `soft_limit` | `number` | `1500`  | The amount of characters the script will start trying to break-up your SSML in multiple parts. You can tweak this number to see what works for you. |
| `hard_limit` | `number` | `3000`  | The amount of characters the script should stay below for maximum size per SSML part. If any batch size goes above this, the script will error. This hard limit is the character limit of the AWS or Google API you are using. |
| `break_paragraphs_above_hard_limit` | `boolean` | `false` | Set to `true` to allow the script to break up large paragraphs by removing the `<p>` and replacing the `</p>` with a `<break strength="x-strong" />` (for `aws`) or `<break strength="x-weak" />` (for `google`). Which results in the same pause. Requires option `synthesizer` to be set. |
| `extra_split_chars` | `string` | `,;.` | Characters that can be used as split markers for plain text.

### About: synthesizer
By using the option `synthesizer: 'google'` the library will include counting SSML tags characters to determine the best possible split moment. This makes the library also work with Google's Text to Speech API.

For example:
`<speak><p>some text</p></speak>`

The default behaviour would count that as 9 characters, which is fine for AWS Polly, but not for Google's Text to Speech API.

With `synthesizer: 'google'` it will be count as 31 characters, [just like Google's Text to Speech API counts it](https://cloud.google.com/text-to-speech/pricing?hl=en).

This should prevent you from seeing this error when using Google's Text to Speech API:

```bash
INVALID_ARGUMENT: 5000 characters limit exceeded.
```

### About: breakParagraphsAboveHardLimit
By adding the option `break_paragraphs_above_hard_limit: True` you allow the script to break up large paragraphs by removing the `<p>` and replacing the `</p>` with a `<break strength="x-strong" />` for AWS or `<break strength="x-weak" />` for Google. Which results in the same pause. This allows the library to properly split large paragraphs.

Using this option will result in 20 more characters, per paragraph, to your usage when using Google's Text to Speech API.

If you work with large paragraphs and you do not use this option, you might run into errors like `SSML tag appeared to be too long`.

Using this option is recommended when you have SSML length that goes above the `hard_limit`.

### Recommended options
#### AWS
```python
SSMLSplit(soft_limit=2000, hard_limit=3000, synthesizer='aws', break_paragraphs_above_hard_limit=True).split(ssml='text')
```

#### Google
```python
SSMLSplit(soft_limit=4000, hard_limit=5000, synthesizer='google', break_paragraphs_above_hard_limit=True).split(ssml='text')
```

## About
The [ssml-split](https://github.com/jvandenaardweg/ssml-split) by [@jvandenaardweg](https://github.com/jvandenaardweg) is available for TypeScript so as such lib was needed to split using Python, migrated to Python 3.9.

### Changes compared to `ssml-split`:
* Ported code from Javascript to Python 3.9
* Added `wrap_with_speak` option to control if wrapping in <speak> tag if resulting strings is needed.

