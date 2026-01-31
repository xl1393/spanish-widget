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
let form = ""
let meaning = ""

if (showVerb && data.verbs.length > 0) {
  const verb = randomItem(data.verbs)
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
    form = `${tense}, ${person}: ${verb[tense][person]}`
  }
} else {
  // If not verb, decide between noun and adjective
  // Give adjectives a fair chance if nouns list is huge? 
  // Code originally: 50% chance pool select.
  const isNoun = Math.random() < 0.5
  const pool = isNoun ? data.nouns : data.adjectives
  
  if (pool && pool.length > 0) {
    const word = randomItem(pool)
    lemma = word.lemma
    meaning = word.meaning

    if (isNoun) {
      if (word.plural) {
         form = `plural: ${word.plural}`
      } else {
         form = word.gender ? `gender: ${word.gender}` : ""
      }
    } else {
      // Adjective
      if (word.forms) {
        // Pick a random form key (masculine, feminine, plural)
        const formKeys = Object.keys(word.forms)
        const randomKey = randomItem(formKeys)
        form = `forms, ${randomKey}: ${word.forms[randomKey]}`
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

// 2. Form (if exists)
if (form) {
  const formText = widget.addText(form)
  formText.font = Font.systemFont(14)
  formText.textColor = Color.lightGray()
}

// 3. Meaning
const meaningText = widget.addText(meaning)
meaningText.font = Font.systemFont(14)
meaningText.textColor = Color.gray()

// ---------- 刷新时间 ----------
widget.refreshAfterDate = new Date(Date.now() + REFRESH_HOURS * 60 * 60 * 1000)

// ---------- 显示 ----------
if (config.runsInWidget) {
  Script.setWidget(widget)
} else {
  widget.presentSmall()
}
Script.complete()