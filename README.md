<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تحدي ألعاب الفيديو - العب وتعلم!</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #ff6f61, #ffcc00);
            color: #fff;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            text-align: center;
        }

        .container {
            background: rgba(255, 255, 255, 0.2);
            padding: 2rem;
            border-radius: 15px;
            box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.3);
            max-width: 500px;
            width: 100%;
        }

        h1 {
            font-size: 2.5rem;
            margin-bottom: 1rem;
        }

        .puzzle {
            font-size: 1.5rem;
            margin: 1.5rem 0;
            padding: 1rem;
            background: rgba(255, 255, 255, 0.3);
            border-radius: 10px;
        }

        .options {
            display: flex;
            flex-direction: column;
            gap: 0.5rem;
        }

        .option {
            background: rgba(255, 255, 255, 0.3);
            padding: 0.75rem;
            border-radius: 5px;
            cursor: pointer;
            transition: background 0.3s ease;
        }

        .option:hover {
            background: rgba(255, 255, 255, 0.5);
        }

        .button {
            background: #2575fc;
            color: #fff;
            border: none;
            padding: 0.75rem 1.5rem;
            font-size: 1rem;
            border-radius: 5px;
            cursor: pointer;
            transition: background 0.3s ease;
        }

        .button:hover {
            background: #1a5bbf;
        }

        .message {
            font-size: 1.2rem;
            margin-top: 1rem;
        }

        .score {
            font-size: 1.2rem;
            margin-top: 1rem;
        }

        .timer {
            font-size: 1.2rem;
            margin-top: 1rem;
        }

        .start-screen, .lose-screen {
            text-align: center;
        }

        .start-screen h1, .lose-screen h1 {
            font-size: 2.5rem;
            margin-bottom: 1.5rem;
        }

        .start-screen .button, .lose-screen .button {
            margin: 0.5rem;
        }

        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div class="container start-screen" id="start-screen">
        <h1>تحدي ألعاب الفيديو 🎮</h1>
        <p>اختر مستوى الصعوبة:</p>
        <button class="button" id="easy-button">سهل</button>
        <button class="button" id="medium-button">متوسط</button>
        <button class="button" id="hard-button">صعب</button>
    </div>

    <div class="container game-screen hidden" id="game-screen">
        <h1>تحدي ألعاب الفيديو 🎮</h1>
        <div class="puzzle" id="puzzle">...</div>
        <div class="options" id="options"></div>
        <div class="message" id="message"></div>
        <div class="score">النقاط: <span id="score">0</span></div>
        <div class="timer" id="timer"></div>
    </div>

    <div class="container lose-screen hidden" id="lose-screen">
        <h1>انتهى الوقت! 😢</h1>
        <p>نقاطك النهائية: <span id="final-score">0</span></p>
        <button class="button" id="restart-button">العب مرة أخرى</button>
    </div>

    <audio id="lose-sound" src="https://www.soundjay.com/misc/sounds/fail-trombone-01.mp3" preload="auto"></audio>

    <script>
        // مجموعة كبيرة من الأسئلة عن الألعاب
        const puzzles = [
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة البقاء على قيد الحياة' وتتضمن بناء منازل من الكتل؟", answer: "Minecraft", options: ["Fortnite", "Minecraft", "Among Us"] },
            { question: "ما هي اللعبة التي تعرف بـ 'معركة رويال' وتتضمن القفز من الحافلة؟", answer: "Fortnite", options: ["Fortnite", "PUBG", "Call of Duty"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة التخفي' وتتضمن مهمات كـ 'Imposter'؟", answer: "Among Us", options: ["Among Us", "Roblox", "Minecraft"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة العالم المفتوح' وتتضمن مهمات سرقة السيارات؟", answer: "GTA V", options: ["GTA V", "Red Dead Redemption", "Cyberpunk 2077"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة البوكيمون' وتتضمن اصطياد المخلوقات؟", answer: "Pokémon GO", options: ["Pokémon GO", "Clash Royale", "Candy Crush"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة المزرعة' وتتضمن زراعة المحاصيل وتربية الحيوانات؟", answer: "Stardew Valley", options: ["Stardew Valley", "Farmville", "Animal Crossing"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الزومبي' وتتضمن البقاء على قيد الحياة في عالم مليء بالزومبي؟", answer: "Plants vs. Zombies", options: ["Plants vs. Zombies", "Left 4 Dead", "Resident Evil"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة السيارات' وتتضمن سباقات سيارات عالية السرعة؟", answer: "Need for Speed", options: ["Need for Speed", "Forza Horizon", "Gran Turismo"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة القتال' وتتضمن شخصيات مثل 'ريو' و'سكوربيون'؟", answer: "Mortal Kombat", options: ["Mortal Kombat", "Street Fighter", "Tekken"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الرعب' وتتضمن شخصية تدعى 'جيسون'؟", answer: "Friday the 13th", options: ["Friday the 13th", "Silent Hill", "Outlast"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة المغامرات' وتتضمن شخصية تدعى 'لارا كروفت'؟", answer: "Tomb Raider", options: ["Tomb Raider", "Uncharted", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماستر تشيف'؟", answer: "Halo", options: ["Halo", "Call of Duty", "Battlefield"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريتوس'؟", answer: "God of War", options: ["God of War", "Dark Souls", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ناثان دريك'؟", answer: "Uncharted", options: ["Uncharted", "Tomb Raider", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'إيزيو'؟", answer: "Assassin's Creed", options: ["Assassin's Creed", "Watch Dogs", "Far Cry"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جيرالت أوف ريفيا'؟", answer: "The Witcher", options: ["The Witcher", "Dark Souls", "Skyrim"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آلوي'؟", answer: "Horizon Zero Dawn", options: ["Horizon Zero Dawn", "The Last of Us", "God of War"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جويل'؟", answer: "The Last of Us", options: ["The Last of Us", "Uncharted", "Tomb Raider"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آرثر مورغان'؟", answer: "Red Dead Redemption", options: ["Red Dead Redemption", "GTA V", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيتو'؟", answer: "Ghost of Tsushima", options: ["Ghost of Tsushima", "Sekiro", "Nioh"] },
               { question: "ما هي اللعبة التي تعرف بـ 'لعبة البقاء على قيد الحياة' وتتضمن بناء منازل من الكتل؟", answer: "Minecraft", options: ["Fortnite", "Minecraft", "Among Us"] },
            { question: "ما هي اللعبة التي تعرف بـ 'معركة رويال' وتتضمن القفز من الحافلة؟", answer: "Fortnite", options: ["Fortnite", "PUBG", "Call of Duty"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة التخفي' وتتضمن مهمات كـ 'Imposter'؟", answer: "Among Us", options: ["Among Us", "Roblox", "Minecraft"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة العالم المفتوح' وتتضمن مهمات سرقة السيارات؟", answer: "GTA V", options: ["GTA V", "Red Dead Redemption", "Cyberpunk 2077"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة البوكيمون' وتتضمن اصطياد المخلوقات؟", answer: "Pokémon GO", options: ["Pokémon GO", "Clash Royale", "Candy Crush"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة المزرعة' وتتضمن زراعة المحاصيل وتربية الحيوانات؟", answer: "Stardew Valley", options: ["Stardew Valley", "Farmville", "Animal Crossing"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الزومبي' وتتضمن البقاء على قيد الحياة في عالم مليء بالزومبي؟", answer: "Plants vs. Zombies", options: ["Plants vs. Zombies", "Left 4 Dead", "Resident Evil"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة السيارات' وتتضمن سباقات سيارات عالية السرعة؟", answer: "Need for Speed", options: ["Need for Speed", "Forza Horizon", "Gran Turismo"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة القتال' وتتضمن شخصيات مثل 'ريو' و'سكوربيون'؟", answer: "Mortal Kombat", options: ["Mortal Kombat", "Street Fighter", "Tekken"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الرعب' وتتضمن شخصية تدعى 'جيسون'؟", answer: "Friday the 13th", options: ["Friday the 13th", "Silent Hill", "Outlast"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة المغامرات' وتتضمن شخصية تدعى 'لارا كروفت'؟", answer: "Tomb Raider", options: ["Tomb Raider", "Uncharted", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماستر تشيف'؟", answer: "Halo", options: ["Halo", "Call of Duty", "Battlefield"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريتوس'؟", answer: "God of War", options: ["God of War", "Dark Souls", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ناثان دريك'؟", answer: "Uncharted", options: ["Uncharted", "Tomb Raider", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'إيزيو'؟", answer: "Assassin's Creed", options: ["Assassin's Creed", "Watch Dogs", "Far Cry"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جيرالت أوف ريفيا'؟", answer: "The Witcher", options: ["The Witcher", "Dark Souls", "Skyrim"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آلوي'؟", answer: "Horizon Zero Dawn", options: ["Horizon Zero Dawn", "The Last of Us", "God of War"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جويل'؟", answer: "The Last of Us", options: ["The Last of Us", "Uncharted", "Tomb Raider"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آرثر مورغان'؟", answer: "Red Dead Redemption", options: ["Red Dead Redemption", "GTA V", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيتو'؟", answer: "Ghost of Tsushima", options: ["Ghost of Tsushima", "Sekiro", "Nioh"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'لينك'؟", answer: "The Legend of Zelda", options: ["The Legend of Zelda", "Super Mario", "Dark Souls"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماريو'؟", answer: "Super Mario", options: ["Super Mario", "Sonic the Hedgehog", "Donkey Kong"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سونيك'؟", answer: "Sonic the Hedgehog", options: ["Sonic the Hedgehog", "Super Mario", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريش'؟", answer: "Crash Bandicoot", options: ["Crash Bandicoot", "Spyro", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سبايرو'؟", answer: "Spyro", options: ["Spyro", "Crash Bandicoot", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'راتشيت وكلانك'؟", answer: "Ratchet & Clank", options: ["Ratchet & Clank", "Spyro", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيرا'؟", answer: "Gears of War", options: ["Gears of War", "Halo", "Call of Duty"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماكس باين'؟", answer: "Max Payne", options: ["Max Payne", "Alan Wake", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آلان ويك'؟", answer: "Alan Wake", options: ["Alan Wake", "Max Payne", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جيسي فادن'؟", answer: "Control", options: ["Control", "Alan Wake", "Max Payne"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كايت'؟", answer: "Tomb Raider", options: ["Tomb Raider", "Uncharted", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'إيلوي'؟", answer: "Horizon Zero Dawn", options: ["Horizon Zero Dawn", "The Last of Us", "God of War"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جويل'؟", answer: "The Last of Us", options: ["The Last of Us", "Uncharted", "Tomb Raider"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آرثر مورغان'؟", answer: "Red Dead Redemption", options: ["Red Dead Redemption", "GTA V", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيتو'؟", answer: "Ghost of Tsushima", options: ["Ghost of Tsushima", "Sekiro", "Nioh"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'لينك'؟", answer: "The Legend of Zelda", options: ["The Legend of Zelda", "Super Mario", "Dark Souls"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماريو'؟", answer: "Super Mario", options: ["Super Mario", "Sonic the Hedgehog", "Donkey Kong"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سونيك'؟", answer: "Sonic the Hedgehog", options: ["Sonic the Hedgehog", "Super Mario", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريش'؟", answer: "Crash Bandicoot", options: ["Crash Bandicoot", "Spyro", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سبايرو'؟", answer: "Spyro", options: ["Spyro", "Crash Bandicoot", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'راتشيت وكلانك'؟", answer: "Ratchet & Clank", options: ["Ratchet & Clank", "Spyro", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيرا'؟", answer: "Gears of War", options: ["Gears of War", "Halo", "Call of Duty"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماكس باين'؟", answer: "Max Payne", options: ["Max Payne", "Alan Wake", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آلان ويك'؟", answer: "Alan Wake", options: ["Alan Wake", "Max Payne", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جيسي فادن'؟", answer: "Control", options: ["Control", "Alan Wake", "Max Payne"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كايت'؟", answer: "Tomb Raider", options: ["Tomb Raider", "Uncharted", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'إيلوي'؟", answer: "Horizon Zero Dawn", options: ["Horizon Zero Dawn", "The Last of Us", "God of War"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جويل'؟", answer: "The Last of Us", options: ["The Last of Us", "Uncharted", "Tomb Raider"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آرثر مورغان'؟", answer: "Red Dead Redemption", options: ["Red Dead Redemption", "GTA V", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيتو'؟", answer: "Ghost of Tsushima", options: ["Ghost of Tsushima", "Sekiro", "Nioh"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'لينك'؟", answer: "The Legend of Zelda", options: ["The Legend of Zelda", "Super Mario", "Dark Souls"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماريو'؟", answer: "Super Mario", options: ["Super Mario", "Sonic the Hedgehog", "Donkey Kong"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سونيك'؟", answer: "Sonic the Hedgehog", options: ["Sonic the Hedgehog", "Super Mario", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريش'؟", answer: "Crash Bandicoot", options: ["Crash Bandicoot", "Spyro", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سبايرو'؟", answer: "Spyro", options: ["Spyro", "Crash Bandicoot", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'راتشيت وكلانك'؟", answer: "Ratchet & Clank", options: ["Ratchet & Clank", "Spyro", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيرا'؟", answer: "Gears of War", options: ["Gears of War", "Halo", "Call of Duty"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماكس باين'؟", answer: "Max Payne", options: ["Max Payne", "Alan Wake", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آلان ويك'؟", answer: "Alan Wake", options: ["Alan Wake", "Max Payne", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جيسي فادن'؟", answer: "Control", options: ["Control", "Alan Wake", "Max Payne"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كايت'؟", answer: "Tomb Raider", options: ["Tomb Raider", "Uncharted", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'إيلوي'؟", answer: "Horizon Zero Dawn", options: ["Horizon Zero Dawn", "The Last of Us", "God of War"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جويل'؟", answer: "The Last of Us", options: ["The Last of Us", "Uncharted", "Tomb Raider"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آرثر مورغان'؟", answer: "Red Dead Redemption", options: ["Red Dead Redemption", "GTA V", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيتو'؟", answer: "Ghost of Tsushima", options: ["Ghost of Tsushima", "Sekiro", "Nioh"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'لينك'؟", answer: "The Legend of Zelda", options: ["The Legend of Zelda", "Super Mario", "Dark Souls"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماريو'؟", answer: "Super Mario", options: ["Super Mario", "Sonic the Hedgehog", "Donkey Kong"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سونيك'؟", answer: "Sonic the Hedgehog", options: ["Sonic the Hedgehog", "Super Mario", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريش'؟", answer: "Crash Bandicoot", options: ["Crash Bandicoot", "Spyro", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سبايرو'؟", answer: "Spyro", options: ["Spyro", "Crash Bandicoot", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'راتشيت وكلانك'؟", answer: "Ratchet & Clank", options: ["Ratchet & Clank", "Spyro", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيرا'؟", answer: "Gears of War", options: ["Gears of War", "Halo", "Call of Duty"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماكس باين'؟", answer: "Max Payne", options: ["Max Payne", "Alan Wake", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آلان ويك'؟", answer: "Alan Wake", options: ["Alan Wake", "Max Payne", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جيسي فادن'؟", answer: "Control", options: ["Control", "Alan Wake", "Max Payne"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كايت'؟", answer: "Tomb Raider", options: ["Tomb Raider", "Uncharted", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'إيلوي'؟", answer: "Horizon Zero Dawn", options: ["Horizon Zero Dawn", "The Last of Us", "God of War"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جويل'؟", answer: "The Last of Us", options: ["The Last of Us", "Uncharted", "Tomb Raider"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آرثر مورغان'؟", answer: "Red Dead Redemption", options: ["Red Dead Redemption", "GTA V", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيتو'؟", answer: "Ghost of Tsushima", options: ["Ghost of Tsushima", "Sekiro", "Nioh"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'لينك'؟", answer: "The Legend of Zelda", options: ["The Legend of Zelda", "Super Mario", "Dark Souls"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماريو'؟", answer: "Super Mario", options: ["Super Mario", "Sonic the Hedgehog", "Donkey Kong"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سونيك'؟", answer: "Sonic the Hedgehog", options: ["Sonic the Hedgehog", "Super Mario", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريش'؟", answer: "Crash Bandicoot", options: ["Crash Bandicoot", "Spyro", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سبايرو'؟", answer: "Spyro", options: ["Spyro", "Crash Bandicoot", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'راتشيت وكلانك'؟", answer: "Ratchet & Clank", options: ["Ratchet & Clank", "Spyro", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيرا'؟", answer: "Gears of War", options: ["Gears of War", "Halo", "Call of Duty"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماكس باين'؟", answer: "Max Payne", options: ["Max Payne", "Alan Wake", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آلان ويك'؟", answer: "Alan Wake", options: ["Alan Wake", "Max Payne", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جيسي فادن'؟", answer: "Control", options: ["Control", "Alan Wake", "Max Payne"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كايت'؟", answer: "Tomb Raider", options: ["Tomb Raider", "Uncharted", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'إيلوي'؟", answer: "Horizon Zero Dawn", options: ["Horizon Zero Dawn", "The Last of Us", "God of War"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جويل'؟", answer: "The Last of Us", options: ["The Last of Us", "Uncharted", "Tomb Raider"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آرثر مورغان'؟", answer: "Red Dead Redemption", options: ["Red Dead Redemption", "GTA V", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيتو'؟", answer: "Ghost of Tsushima", options: ["Ghost of Tsushima", "Sekiro", "Nioh"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'لينك'؟", answer: "The Legend of Zelda", options: ["The Legend of Zelda", "Super Mario", "Dark Souls"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماريو'؟", answer: "Super Mario", options: ["Super Mario", "Sonic the Hedgehog", "Donkey Kong"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سونيك'؟", answer: "Sonic the Hedgehog", options: ["Sonic the Hedgehog", "Super Mario", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريش'؟", answer: "Crash Bandicoot", options: ["Crash Bandicoot", "Spyro", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سبايرو'؟", answer: "Spyro", options: ["Spyro", "Crash Bandicoot", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'راتشيت وكلانك'؟", answer: "Ratchet & Clank", options: ["Ratchet & Clank", "Spyro", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيرا'؟", answer: "Gears of War", options: ["Gears of War", "Halo", "Call of Duty"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماكس باين'؟", answer: "Max Payne", options: ["Max Payne", "Alan Wake", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آلان ويك'؟", answer: "Alan Wake", options: ["Alan Wake", "Max Payne", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جيسي فادن'؟", answer: "Control", options: ["Control", "Alan Wake", "Max Payne"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كايت'؟", answer: "Tomb Raider", options: ["Tomb Raider", "Uncharted", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'إيلوي'؟", answer: "Horizon Zero Dawn", options: ["Horizon Zero Dawn", "The Last of Us", "God of War"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جويل'؟", answer: "The Last of Us", options: ["The Last of Us", "Uncharted", "Tomb Raider"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آرثر مورغان'؟", answer: "Red Dead Redemption", options: ["Red Dead Redemption", "GTA V", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيتو'؟", answer: "Ghost of Tsushima", options: ["Ghost of Tsushima", "Sekiro", "Nioh"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'لينك'؟", answer: "The Legend of Zelda", options: ["The Legend of Zelda", "Super Mario", "Dark Souls"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماريو'؟", answer: "Super Mario", options: ["Super Mario", "Sonic the Hedgehog", "Donkey Kong"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سونيك'؟", answer: "Sonic the Hedgehog", options: ["Sonic the Hedgehog", "Super Mario", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريش'؟", answer: "Crash Bandicoot", options: ["Crash Bandicoot", "Spyro", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سبايرو'؟", answer: "Spyro", options: ["Spyro", "Crash Bandicoot", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'راتشيت وكلانك'؟", answer: "Ratchet & Clank", options: ["Ratchet & Clank", "Spyro", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيرا'؟", answer: "Gears of War", options: ["Gears of War", "Halo", "Call of Duty"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماكس باين'؟", answer: "Max Payne", options: ["Max Payne", "Alan Wake", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آلان ويك'؟", answer: "Alan Wake", options: ["Alan Wake", "Max Payne", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جيسي فادن'؟", answer: "Control", options: ["Control", "Alan Wake", "Max Payne"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كايت'؟", answer: "Tomb Raider", options: ["Tomb Raider", "Uncharted", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'إيلوي'؟", answer: "Horizon Zero Dawn", options: ["Horizon Zero Dawn", "The Last of Us", "God of War"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جويل'؟", answer: "The Last of Us", options: ["The Last of Us", "Uncharted", "Tomb Raider"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آرثر مورغان'؟", answer: "Red Dead Redemption", options: ["Red Dead Redemption", "GTA V", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيتو'؟", answer: "Ghost of Tsushima", options: ["Ghost of Tsushima", "Sekiro", "Nioh"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'لينك'؟", answer: "The Legend of Zelda", options: ["The Legend of Zelda", "Super Mario", "Dark Souls"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماريو'؟", answer: "Super Mario", options: ["Super Mario", "Sonic the Hedgehog", "Donkey Kong"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سونيك'؟", answer: "Sonic the Hedgehog", options: ["Sonic the Hedgehog", "Super Mario", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريش'؟", answer: "Crash Bandicoot", options: ["Crash Bandicoot", "Spyro", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سبايرو'؟", answer: "Spyro", options: ["Spyro", "Crash Bandicoot", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'راتشيت وكلانك'؟", answer: "Ratchet & Clank", options: ["Ratchet & Clank", "Spyro", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيرا'؟", answer: "Gears of War", options: ["Gears of War", "Halo", "Call of Duty"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماكس باين'؟", answer: "Max Payne", options: ["Max Payne", "Alan Wake", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آلان ويك'؟", answer: "Alan Wake", options: ["Alan Wake", "Max Payne", "Control"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جيسي فادن'؟", answer: "Control", options: ["Control", "Alan Wake", "Max Payne"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كايت'؟", answer: "Tomb Raider", options: ["Tomb Raider", "Uncharted", "Assassin's Creed"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'إيلوي'؟", answer: "Horizon Zero Dawn", options: ["Horizon Zero Dawn", "The Last of Us", "God of War"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'جويل'؟", answer: "The Last of Us", options: ["The Last of Us", "Uncharted", "Tomb Raider"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'آرثر مورغان'؟", answer: "Red Dead Redemption", options: ["Red Dead Redemption", "GTA V", "The Witcher"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيتو'؟", answer: "Ghost of Tsushima", options: ["Ghost of Tsushima", "Sekiro", "Nioh"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'لينك'؟", answer: "The Legend of Zelda", options: ["The Legend of Zelda", "Super Mario", "Dark Souls"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'ماريو'؟", answer: "Super Mario", options: ["Super Mario", "Sonic the Hedgehog", "Donkey Kong"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سونيك'؟", answer: "Sonic the Hedgehog", options: ["Sonic the Hedgehog", "Super Mario", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كريش'؟", answer: "Crash Bandicoot", options: ["Crash Bandicoot", "Spyro", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'سبايرو'؟", answer: "Spyro", options: ["Spyro", "Crash Bandicoot", "Ratchet & Clank"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'راتشيت وكلانك'؟", answer: "Ratchet & Clank", options: ["Ratchet & Clank", "Spyro", "Crash Bandicoot"] },
            { question: "ما هي اللعبة التي تعرف بـ 'لعبة الأكشن' وتتضمن شخصية تدعى 'كيرا'؟", answer: "Gears of War", options: ["Gears of War", "Halo", "Call of Duty"] },
            // يمكن إضافة المزيد من الأسئلة هنا...
        ];

        let currentPuzzleIndex = 0;
        let score = 0;
        let timer;
        let timeLeft;
        let difficulty = "easy";

        const startScreen = document.getElementById('start-screen');
        const gameScreen = document.getElementById('game-screen');
        const loseScreen = document.getElementById('lose-screen');
        const puzzleElement = document.getElementById('puzzle');
        const optionsElement = document.getElementById('options');
        const messageElement = document.getElementById('message');
        const scoreElement = document.getElementById('score');
        const timerElement = document.getElementById('timer');
        const finalScoreElement = document.getElementById('final-score');
        const loseSound = document.getElementById('lose-sound');

        const easyButton = document.getElementById('easy-button');
        const mediumButton = document.getElementById('medium-button');
        const hardButton = document.getElementById('hard-button');
        const restartButton = document.getElementById('restart-button');

        function startGame(selectedDifficulty) {
            difficulty = selectedDifficulty;
            startScreen.classList.add('hidden');
            gameScreen.classList.remove('hidden');
            loseScreen.classList.add('hidden');
            score = 0;
            scoreElement.textContent = score;
            loadRandomPuzzle();
        }

        function loadRandomPuzzle() {
            currentPuzzleIndex = Math.floor(Math.random() * puzzles.length);
            const puzzle = puzzles[currentPuzzleIndex];
            puzzleElement.textContent = puzzle.question;
            optionsElement.innerHTML = puzzle.options.map(option => `
                <div class="option" onclick="checkAnswer('${option}')">${option}</div>
            `).join('');
            messageElement.textContent = '';
            startTimer();
        }

        function checkAnswer(selectedAnswer) {
            clearInterval(timer);
            const correctAnswer = puzzles[currentPuzzleIndex].answer;
            if (selectedAnswer === correctAnswer) {
                messageElement.textContent = "إجابة صحيحة! 🎉";
                score += 10;
                scoreElement.textContent = score;
                loadRandomPuzzle();
            } else {
                showLoseScreen();
            }
        }

        function showLoseScreen() {
            loseSound.play();
            gameScreen.classList.add('hidden');
            loseScreen.classList.remove('hidden');
            finalScoreElement.textContent = score;
        }

        function startTimer() {
            if (difficulty === "medium") {
                timeLeft = 20;
            } else if (difficulty === "hard") {
                timeLeft = 10;
            } else {
                timerElement.textContent = "";
                return;
            }
            timerElement.textContent = `الوقت المتبقي: ${timeLeft} ثانية`;
            timer = setInterval(() => {
                timeLeft--;
                timerElement.textContent = `الوقت المتبقي: ${timeLeft} ثانية`;
                if (timeLeft <= 0) {
                    clearInterval(timer);
                    showLoseScreen();
                }
            }, 1000);
        }

        easyButton.addEventListener('click', () => startGame("easy"));
        mediumButton.addEventListener('click', () => startGame("medium"));
        hardButton.addEventListener('click', () => startGame("hard"));
        restartButton.addEventListener('click', () => startGame(difficulty));
    </script>
</body>
</html>
