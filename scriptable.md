// Spanish A2 Lock Screen Vocabulary Widget

const VOCAB_URL = "https://raw.githubusercontent.com/xl1393/spanish-widget/main/spanish_a2_full_vocab.json"
const REFRESH_HOURS = 2

// ---------- 工具函数 ----------
function randomItem(arr) {
  return arr[Math.floor(Math.random() * arr.length)]
}

// ---------- 读取词库 ----------
const req = new Request(VOCAB_URL)
const data = await req.loadJSON()

// 随机决定显示类型（动词优先）
const showVerb = Math.random() < 0.7

let lemma = ""
let formType = ""
let changedWord = ""
let meaning = ""

// Helper for abbreviations
function abbr(str) {
  const map = {
    "present": "pres.",
    "past": "pas.",
    "nosotros": "nos.",
    "plural": "pl.",
    "masculine": "masc.",
    "feminine": "fem.",
    "gender": "gen.",
    "forms": "form"
  }
  return map[str] || str
}

if (showVerb && data.verbs.length > 0) {
  const verb = randomItem(data.verbs)
  lemma = verb.lemma.split(',')[0] // removing ", v." for display if desired, or keeping it? User said "word".
  // Keeping lemma as is to be safe, but typically clean display is nicer. 
  // User prompt said "word, the form, the changed word, meaning", 
  // but let's stick to the current lemma format "lemma, v." if user doesn't object, 
  // OR actually better to match the prompt "word" -> "servir, v."
  lemma = verb.lemma
  meaning = verb.meaning

  // Try to pick a tense that exists
  let tenses = []
  if (verb.present) tenses.push("present")
  if (verb.past) tenses.push("past")
  
  if (tenses.length > 0) {
    const tense = randomItem(tenses)
    const persons = Object.keys(verb[tense])
    const person = randomItem(persons)
    formType = `${abbr(tense)}, ${abbr(person)}`
    changedWord = verb[tense][person]
  }
} else {
  // If not verb, decide between noun and adjective
  const isNoun = Math.random() < 0.5
  const pool = isNoun ? data.nouns : data.adjectives
  
  if (pool && pool.length > 0) {
    const word = randomItem(pool)
    lemma = word.lemma
    meaning = word.meaning

    if (isNoun) {
      if (word.plural) {
         formType = abbr("plural")
         changedWord = word.plural
      } else {
         formType = abbr("gender")
         changedWord = word.gender || ""
      }
    } else {
      // Adjective
      if (word.forms) {
        // Pick a random form key (masculine, feminine, plural)
        const formKeys = Object.keys(word.forms)
        const randomKey = randomItem(formKeys)
        formType = `${abbr("forms")}, ${abbr(randomKey)}`
        changedWord = word.forms[randomKey]
      }
    }
  }
}

// ---------- 构建 Widget ----------
const widget = new ListWidget()
widget.backgroundColor = Color.black()

// 1. Lemma
const lemmaText = widget.addText(lemma)
lemmaText.font = Font.boldSystemFont(18)
lemmaText.textColor = Color.white()

// 2. Form Type (e.g. "past, yo")
if (formType) {
  const typeText = widget.addText(formType)
  typeText.font = Font.systemFont(12)
  typeText.textColor = Color.gray()
}

// 3. Changed Word (e.g. "serví")
if (changedWord) {
  const wordText = widget.addText(changedWord)
  wordText.font = Font.systemFont(16)
  wordText.textColor = Color.cyan() // Highlight the conjugated/changed word
}

// 4. Meaning
const meaningText = widget.addText(meaning)
meaningText.font = Font.italicSystemFont(12)
meaningText.textColor = Color.lightGray()

// ---------- 刷新时间 ----------
widget.refreshAfterDate = new Date(Date.now() + REFRESH_HOURS * 60 * 60 * 1000)

// ---------- 显示 ----------
if (config.runsInWidget) {
  Script.setWidget(widget)
} else {
  widget.presentSmall()
}
Script.complete()