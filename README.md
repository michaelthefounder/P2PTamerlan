<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<title>P2P Тест</title>
<script src="https://telegram.org/js/telegram-web-app.js" async onload="window.__tgLoaded&&window.__tgLoaded()"></script>
<style>
  /* ====== Цвета берутся из темы Telegram, ниже — запасные ====== */
  :root{
    --bg: var(--tg-theme-bg-color, #ffffff);
    --bg2: var(--tg-theme-secondary-bg-color, #f2f3f5);
    --text: var(--tg-theme-text-color, #111111);
    --hint: var(--tg-theme-hint-color, #8a8f98);
    --accent: var(--tg-theme-button-color, #2ea6ff);
    --accent-text: var(--tg-theme-button-text-color, #ffffff);
    --ok: #2fb36a;
    --bad: #e5484d;
  }
  @media (prefers-color-scheme: dark){
    :root:not(.tg){
      --bg:#17212b; --bg2:#0e1621; --text:#f5f5f5; --hint:#7d8b99;
    }
  }
  *{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
  html,body{margin:0;background:var(--bg);color:var(--text);
    font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif;font-size:16px;line-height:1.4}
  .wrap{max-width:560px;margin:0 auto;padding:16px 16px 32px;min-height:100vh;display:flex;flex-direction:column}
  .card{background:var(--bg2);border-radius:16px;padding:18px}
  h1{font-size:24px;margin:8px 0 6px}
  h2{font-size:19px;margin:0 0 14px}
  p{margin:0 0 10px}
  .hint{color:var(--hint);font-size:14px}
  .emoji{font-size:56px;text-align:center;margin:16px 0 4px}
  .btn{display:block;width:100%;border:0;border-radius:12px;padding:14px;font-size:16px;font-weight:600;
    background:var(--accent);color:var(--accent-text);cursor:pointer;margin-top:12px}
  .btn.ghost{background:transparent;color:var(--accent);border:1.5px solid var(--accent)}
  .btn:disabled{opacity:.45}
  .meta{display:flex;justify-content:space-between;align-items:center;font-size:14px;color:var(--hint);margin-bottom:8px}
  .bar{height:6px;background:var(--bg2);border-radius:3px;overflow:hidden;margin-bottom:16px}
  .bar>i{display:block;height:100%;background:var(--accent);transition:width .3s}
  .opt{display:block;width:100%;text-align:left;border:1.5px solid transparent;background:var(--bg);color:var(--text);
    border-radius:12px;padding:13px 14px;margin-top:10px;font-size:15px;cursor:pointer}
  .opt.ok{border-color:var(--ok);background:rgba(47,179,106,.15)}
  .opt.bad{border-color:var(--bad);background:rgba(229,72,77,.15)}
  .opt:disabled{cursor:default}
  .expl{margin-top:14px;padding:12px 14px;border-radius:12px;background:var(--bg);font-size:14px}
  .expl b{display:block;margin-bottom:4px}
  .score{font-size:48px;font-weight:800;text-align:center;margin:4px 0}
  .stats{display:flex;gap:10px;margin:14px 0}
  .stats div{flex:1;background:var(--bg);border-radius:12px;padding:10px;text-align:center}
  .stats b{display:block;font-size:20px}
  .mist{border-top:1px solid rgba(128,128,128,.25);padding:12px 0;font-size:14px}
  .mist:first-child{border-top:0}
  .mist .q{font-weight:600;margin-bottom:4px}
  .mist .y{color:var(--bad)} .mist .c{color:var(--ok)}
  .timer{font-variant-numeric:tabular-nums}
  .timer.low{color:var(--bad);font-weight:700}
  .hidden{display:none!important}
</style>
</head>
<body>
<div class="wrap" id="app"><p class="hint" style="text-align:center;margin-top:40vh">Загрузка…</p></div>
<script>
window.onerror = function(msg, src, line){
  var a = document.getElementById("app");
  if (a) a.innerHTML = '<div class="card" style="margin-top:20px"><b>Ошибка в приложении</b><p class="hint">' + String(msg) + ' (строка ' + line + ')</p><p class="hint">Пришлите этот текст разработчику.</p></div>';
};
</script>

<script>
/* =====================================================================
   НАСТРОЙКИ — меняйте под себя
   ===================================================================== */
const CONFIG = {
  title: "Тест по P2P-торговле",
  channelName: "мой канал",          // название вашего канала
  teacherUsername: "",               // ваш @username без @ — для кнопки «Отправить преподавателю»
  questionsPerTest: 15,              // сколько вопросов в одной попытке (берутся случайно)
  secondsPerQuestion: 45,            // таймер на вопрос; 0 — без таймера
  passPercent: 70,                   // проходной балл, %
  shuffleOptions: true               // перемешивать варианты ответов
};

/* =====================================================================
   ВОПРОСЫ — добавляйте свои по образцу.
   correct — номер правильного варианта, считая с 0.
   ===================================================================== */
const QUESTIONS = [
  {
    q: "Что такое P2P-торговля на криптобирже?",
    options: [
      "Обмен криптовалюты напрямую между пользователями, биржа выступает гарантом",
      "Покупка криптовалюты у самой биржи по фиксированному курсу",
      "Торговля фьючерсами с кредитным плечом",
      "Перевод крипты между своими кошельками"
    ],
    correct: 0,
    explain: "В P2P вы торгуете с другим человеком, а биржа лишь замораживает монеты продавца (эскроу) до завершения сделки."
  },
  {
    q: "Что делает эскроу в P2P-сделке?",
    options: [
      "Конвертирует фиат в крипту автоматически",
      "Блокирует монеты продавца до подтверждения оплаты",
      "Проверяет банковскую карту покупателя",
      "Гарантирует лучший курс"
    ],
    correct: 1,
    explain: "Эскроу «замораживает» монеты продавца на время сделки, чтобы он не мог продать их дважды, а покупатель был защищён."
  },
  {
    q: "Когда продавцу можно отпускать монеты?",
    options: [
      "Сразу после того, как покупатель нажал «Оплачено»",
      "Когда покупатель прислал скриншот перевода",
      "Только когда деньги реально поступили на счёт — проверено в своём банке",
      "Через 15 минут после открытия ордера"
    ],
    correct: 2,
    explain: "Кнопка «Оплачено» и скриншоты ничего не гарантируют — их легко подделать. Проверяйте поступление только в своём банковском приложении."
  },
  {
    q: "Покупатель прислал скриншот оплаты, но деньги не пришли. Ваши действия?",
    options: [
      "Отпустить монеты — скриншот выглядит настоящим",
      "Не отпускать монеты, перепроверить банк, при необходимости открыть апелляцию",
      "Отменить ордер",
      "Написать покупателю в личку в Telegram"
    ],
    correct: 1,
    explain: "Пока деньги не на счёте — монеты не отпускаем. Если покупатель настаивает, подключайте поддержку через апелляцию."
  },
  {
    q: "Оплата пришла, но ФИО отправителя не совпадает с ФИО в профиле покупателя. Что делать?",
    options: [
      "Отпустить монеты — деньги ведь пришли",
      "Попросить доплатить комиссию за риск",
      "Не отпускать, вернуть деньги отправителю и решать вопрос через апелляцию",
      "Игнорировать покупателя"
    ],
    correct: 2,
    explain: "Платёж от третьего лица — главный признак мошеннической схемы. Деньги возвращают тому, кто отправил, а сделку решают через поддержку."
  },
  {
    q: "Что такое схема «треугольник» в P2P?",
    options: [
      "Арбитраж между тремя биржами",
      "Мошенник получает крипту от вас, а платит за неё деньгами обманутого третьего человека",
      "Сделка с тремя участниками и общим чатом",
      "Обмен трёх разных валют за одну сделку"
    ],
    correct: 1,
    explain: "Жертва думает, что платит за товар/услугу, а деньги идут вам. Вы отпускаете крипту мошеннику, а потом жертва требует деньги назад — проблемы у вас."
  },
  {
    q: "Контрагент предлагает продолжить общение в Telegram или WhatsApp. Как правильно?",
    options: [
      "Согласиться, так удобнее",
      "Согласиться, если у него высокий рейтинг",
      "Отказаться и общаться только в чате ордера на бирже",
      "Перейти, но скинуть скрины поддержке"
    ],
    correct: 2,
    explain: "Переписка вне биржи не учитывается в апелляции. Вывод в мессенджеры — частый приём мошенников."
  },
  {
    q: "Что такое спред в P2P-торговле?",
    options: [
      "Комиссия банка за перевод",
      "Разница между ценой покупки и ценой продажи актива",
      "Лимит объявления",
      "Время на оплату ордера"
    ],
    correct: 1,
    explain: "Спред — это то, на чём зарабатывает P2P-трейдер: купить дешевле, продать дороже."
  },
  {
    q: "Вы купили 1 000 USDT по 95 ₽ и продали по 96,5 ₽. Комиссий нет. Какая прибыль?",
    options: ["150 ₽", "1 500 ₽", "15 000 ₽", "965 ₽"],
    correct: 1,
    explain: "(96,5 − 95) × 1 000 = 1,5 × 1 000 = 1 500 ₽."
  },
  {
    q: "Спред 1,2 %, оборот за круг — 100 000 ₽. Сколько вы заработаете за один круг (без комиссий)?",
    options: ["120 ₽", "1 200 ₽", "12 000 ₽", "12 ₽"],
    correct: 1,
    explain: "100 000 × 1,2 % = 1 200 ₽."
  },
  {
    q: "Когда покупатель должен нажимать кнопку «Оплачено» / «Я перевёл»?",
    options: [
      "Сразу при открытии ордера, чтобы не истёк таймер",
      "Только после фактического перевода денег",
      "После того, как продавец отпустит монеты",
      "Не обязательно нажимать"
    ],
    correct: 1,
    explain: "Нажатие «Оплачено» без перевода — нарушение правил, за это блокируют аккаунт."
  },
  {
    q: "Можно ли в комментарии к банковскому переводу писать «USDT», «крипта», «биржа»?",
    options: [
      "Да, так прозрачнее",
      "Да, если сумма небольшая",
      "Нет — это может привести к блокировке карты банком",
      "Только по просьбе продавца"
    ],
    correct: 2,
    explain: "Комментарии оставляем пустыми. Упоминание крипты — частая причина вопросов и блокировок со стороны банка."
  },
  {
    q: "Кто такой мейкер в P2P?",
    options: [
      "Тот, кто размещает своё объявление",
      "Тот, кто откликается на чужое объявление",
      "Сотрудник поддержки биржи",
      "Платёжный агент банка"
    ],
    correct: 0,
    explain: "Мейкер создаёт объявление и задаёт цену. Тейкер — тот, кто открывает сделку по чужому объявлению."
  },
  {
    q: "Вы оплатили ордер, таймер вышел, а продавец не отпускает монеты. Что делать?",
    options: [
      "Отменить ордер",
      "Открыть апелляцию и приложить подтверждение оплаты",
      "Оставить негативный отзыв и забыть",
      "Создать новый ордер у того же продавца"
    ],
    correct: 1,
    explain: "Если вы оплатили — ни в коем случае не отменяйте ордер. Открывайте апелляцию с чеком/выпиской."
  },
  {
    q: "На что в первую очередь смотреть при выборе контрагента?",
    options: [
      "На аватарку",
      "На количество сделок, процент завершения и возраст аккаунта",
      "Только на лучшую цену",
      "На наличие эмодзи в описании"
    ],
    correct: 1,
    explain: "Цена — не главное. Опыт и процент успешных сделок снижают риск попасть на мошенника."
  },
  {
    q: "Цена в объявлении заметно лучше рынка, а аккаунт создан вчера. Это скорее…",
    options: [
      "Удачная возможность — надо брать",
      "Повод насторожиться: возможна мошенническая схема",
      "Ошибка биржи",
      "Нормальная ситуация для новичков"
    ],
    correct: 1,
    explain: "Слишком выгодная цена у нового аккаунта — классическая приманка."
  },
  {
    q: "Что такое апелляция?",
    options: [
      "Жалоба на биржу в ЦБ",
      "Обращение в поддержку биржи для решения спора по ордеру",
      "Отмена сделки без последствий",
      "Повышение лимитов аккаунта"
    ],
    correct: 1,
    explain: "Апелляция — спор внутри ордера: поддержка изучает чат и доказательства и решает, кому достанутся монеты."
  },
  {
    q: "Какое поведение повышает риск блокировки карты банком?",
    options: [
      "Редкие переводы с одним контрагентом",
      "Множество входящих переводов от разных людей за короткое время",
      "Оплата коммунальных услуг",
      "Перевод между своими счетами"
    ],
    correct: 1,
    explain: "Большой поток переводов от разных лиц банк может посчитать подозрительным. Распределяйте нагрузку и следите за лимитами."
  },
  {
    q: "Покупатель просит отпустить монеты «авансом», обещая оплатить через 5 минут. Ваши действия?",
    options: [
      "Отпустить — он вежливый",
      "Отпустить половину",
      "Отказать: монеты отпускаются только после поступления денег",
      "Попросить залог на карту"
    ],
    correct: 2,
    explain: "Никаких авансов. Отпущенные монеты вернуть уже нельзя."
  },
  {
    q: "Зачем указывать в объявлении условия сделки (банки, лимиты, правила)?",
    options: [
      "Это обязательное поле для украшения",
      "Чтобы отсеять неподходящих контрагентов и иметь аргумент в апелляции",
      "Чтобы повысить цену",
      "Это ни на что не влияет"
    ],
    correct: 1,
    explain: "Чёткие условия экономят время и защищают вас: поддержка учитывает их при разборе спора."
  }
];

/* =====================================================================
   ЛОГИКА ПРИЛОЖЕНИЯ — менять не нужно
   ===================================================================== */
var tg = null, inTG = false, user = null, userName = "";
function initTelegram(){
  try{
    var w = window.Telegram && window.Telegram.WebApp;
    if (!w || tg) return;
    tg = w; inTG = !!tg.initData;
    tg.ready(); tg.expand();
    if (inTG) document.documentElement.classList.add("tg");
    user = (tg.initDataUnsafe && tg.initDataUnsafe.user) || null;
    userName = user ? [user.first_name, user.last_name].filter(Boolean).join(" ") : "";
    if (!state.qs) startScreen();           // обновить приветствие с именем
  }catch(e){}
}
window.__tgLoaded = initTelegram;

const app = document.getElementById("app");
let state = {};

function haptic(type){ try{ tg && tg.HapticFeedback && tg.HapticFeedback.notificationOccurred(type);}catch(e){} }
function shuffle(a){ a=[...a]; for(let i=a.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[a[i],a[j]]=[a[j],a[i]];} return a; }
function esc(s){ return String(s).replace(/[&<>"]/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;"}[c])); }

function getBest(){ try{ return Number(localStorage.getItem("p2p_best")||0);}catch(e){return 0;} }
function setBest(v){ try{ if(v>getBest()) localStorage.setItem("p2p_best", v);}catch(e){} }

function startScreen(){
  const best = getBest();
  const n = Math.min(CONFIG.questionsPerTest, QUESTIONS.length);
  app.innerHTML = `
    <div class="emoji">📊</div>
    <h1 style="text-align:center">${esc(CONFIG.title)}</h1>
    <p class="hint" style="text-align:center">${userName ? "Привет, "+esc(userName)+"! " : ""}Проверь, как ты усвоил материал канала «${esc(CONFIG.channelName)}».</p>
    <div class="card" style="margin-top:16px">
      <p>📝 Вопросов: <b>${n}</b></p>
      ${CONFIG.secondsPerQuestion ? `<p>⏱ На вопрос: <b>${CONFIG.secondsPerQuestion} сек</b></p>` : ""}
      <p>🎯 Проходной балл: <b>${CONFIG.passPercent}%</b></p>
      ${best ? `<p style="margin:0">🏆 Твой лучший результат: <b>${best}%</b></p>` : ""}
    </div>
    <div style="flex:1"></div>
    <button class="btn" data-act="start">Начать тест</button>`;
}

function startTest(){
  const qs = shuffle(QUESTIONS).slice(0, Math.min(CONFIG.questionsPerTest, QUESTIONS.length)).map(q=>{
    const idx = q.options.map((_,i)=>i);
    const order = CONFIG.shuffleOptions ? shuffle(idx) : idx;
    return Object.assign({}, q, { order: order });
  });
  state = { qs, i:0, answers:[], startedAt: Date.now() };
  showQuestion();
}

let timerId = null;
function showQuestion(){
  clearInterval(timerId);
  const { qs, i } = state;
  const q = qs[i];
  const pct = Math.round(i / qs.length * 100);
  app.innerHTML = `
    <div class="meta"><span>Вопрос ${i+1} из ${qs.length}</span>
      ${CONFIG.secondsPerQuestion ? `<span class="timer" id="timer">⏱ ${CONFIG.secondsPerQuestion}</span>` : ""}</div>
    <div class="bar"><i style="width:${pct}%"></i></div>
    <div class="card">
      <h2>${esc(q.q)}</h2>
      <div id="opts">${q.order.map(oi=>`<button class="opt" data-i="${oi}">${esc(q.options[oi])}</button>`).join("")}</div>
      <div id="expl"></div>
    </div>
    <div style="flex:1"></div>
    <button class="btn hidden" id="next">${i+1 < qs.length ? "Дальше" : "Узнать результат"}</button>`;
  app.querySelectorAll(".opt").forEach(b=>b.onclick=()=>answer(Number(b.dataset.i)));
  document.getElementById("next").onclick=()=>{ state.i++; state.i<state.qs.length ? showQuestion() : resultScreen(); };

  if (CONFIG.secondsPerQuestion){
    let left = CONFIG.secondsPerQuestion;
    const el = document.getElementById("timer");
    timerId = setInterval(()=>{
      left--; el.textContent = "⏱ " + left;
      if (left <= 10) el.classList.add("low");
      if (left <= 0){ clearInterval(timerId); answer(-1); }
    },1000);
  }
}

function answer(chosen){
  clearInterval(timerId);
  const q = state.qs[state.i];
  if (state.answers[state.i] !== undefined) return;
  const ok = chosen === q.correct;
  state.answers[state.i] = chosen;
  haptic(ok ? "success" : "error");
  app.querySelectorAll(".opt").forEach(b=>{
    const oi = Number(b.dataset.i); b.disabled = true;
    if (oi === q.correct) b.classList.add("ok");
    else if (oi === chosen) b.classList.add("bad");
  });
  document.getElementById("expl").innerHTML = `<div class="expl"><b>${ok ? "✅ Верно!" : chosen===-1 ? "⏰ Время вышло" : "❌ Неверно"}</b>${esc(q.explain||"")}</div>`;
  document.getElementById("next").classList.remove("hidden");
}

function resultScreen(){
  const { qs, answers, startedAt } = state;
  const right = qs.filter((q,i)=>answers[i]===q.correct).length;
  const pct = Math.round(right / qs.length * 100);
  const passed = pct >= CONFIG.passPercent;
  const mins = Math.max(1, Math.round((Date.now()-startedAt)/60000));
  setBest(pct);
  haptic(passed ? "success" : "warning");
  state.result = { right, total: qs.length, pct, passed, mins };

  const mistakes = qs.map((q,i)=>({q,a:answers[i]})).filter(x=>x.a!==x.q.correct);
  app.innerHTML = `
    <div class="emoji">${pct>=90?"🏆":passed?"🎉":"📚"}</div>
    <div class="score">${pct}%</div>
    <p style="text-align:center">${pct>=90?"Отлично! Ты готов торговать.":passed?"Тест сдан! Есть что подтянуть.":"Пока не сдан — перечитай материалы и попробуй снова."}</p>
    <div class="stats">
      <div><b style="color:var(--ok)">${right}</b><span class="hint">верно</span></div>
      <div><b style="color:var(--bad)">${qs.length-right}</b><span class="hint">ошибок</span></div>
      <div><b>${mins}</b><span class="hint">мин</span></div>
    </div>
    ${mistakes.length ? `<div class="card"><h2>Разбор ошибок</h2>${mistakes.map(({q,a})=>`
      <div class="mist"><div class="q">${esc(q.q)}</div>
      <div class="y">Твой ответ: ${a===-1?"— (время вышло)":esc(q.options[a])}</div>
      <div class="c">Правильно: ${esc(q.options[q.correct])}</div></div>`).join("")}</div>` : ""}
    <button class="btn" data-act="send">📤 Отправить результат преподавателю</button>
    <button class="btn ghost" data-act="start">Пройти ещё раз</button>`;
}

function resultText(){
  const r = state.result;
  const who = userName ? userName + (user && user.username ? " (@"+user.username+")" : "") : "Ученик";
  return `📊 ${CONFIG.title}\n👤 ${who}\n✅ ${r.right} из ${r.total} — ${r.pct}%\n${r.passed ? "🎯 Сдал" : "📚 Не сдал"} · ⏱ ${r.mins} мин`;
}

function sendResult(){
  const text = resultText();
  // 1) Если приложение открыто кнопкой клавиатуры бота — данные уйдут боту
  if (inTG && tg.sendData && !tg.initDataUnsafe.query_id) {
    try { tg.sendData(JSON.stringify(Object.assign({ type:"p2p_test", text: text }, state.result))); return; } catch(e){}
  }
  // 2) Иначе — открываем личку преподавателя или окно «Поделиться»
  const shareUrl = "https://t.me/share/url?url=" + encodeURIComponent(" ") + "&text=" + encodeURIComponent(text);
  if (CONFIG.teacherUsername){
    try { navigator.clipboard && navigator.clipboard.writeText(text); } catch(e){}
    const url = "https://t.me/" + CONFIG.teacherUsername;
    if (inTG) { tg.showAlert("Результат скопирован. Вставь его в чат с преподавателем.", ()=>tg.openTelegramLink(url)); }
    else { alert("Результат скопирован:\n\n"+text); window.open(url,"_blank"); }
  } else {
    inTG ? tg.openTelegramLink(shareUrl) : window.open(shareUrl,"_blank");
  }
}

app.addEventListener("click", function(e){
  var b = e.target.closest ? e.target.closest("[data-act]") : null;
  if (!b) return;
  if (b.dataset.act === "start") startTest();
  if (b.dataset.act === "send") sendResult();
});

startScreen();
initTelegram();   // если SDK уже успел загрузиться
</script>
</body>
</html>
