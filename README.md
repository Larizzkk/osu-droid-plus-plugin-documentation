# osu!droid+ Plugin API Reference

All plugins are `.lua` files placed in `Downloads/osudroidplus/plugins/`.

---

## Plugin Lifecycle

```lua
name = "My Plugin"         -- REQUIRED: plugin name
version = "1.0.0"          -- semantic version
author = "YourName"
description = "What it does"

function onLoad()          -- called once when plugin loads
function onUnload()        -- called once when plugin unloads
```

## Game Events

```lua
function onGameUpdate(dt, gameTime)     -- every frame (dt=seconds, gameTime=seconds)
function onCircleHit(objectId, accuracy, x, y, endCombo, scoreValue)
function onSliderHit(objectId, scoreType, x, y, endCombo)
function onSliderEnd(objectId, accuracy)
function onSpinnerStart(objectId)
function onSpinnerHit(objectId, scoreValue)
function onSpinnerEnd(objectId)
function onBeatmapLoaded(name, artist, difficulty, objectCount)
function onBeatmapFinished(totalScore, maxCombo, accuracy, grade)
function onKeyDown(key, pressed)        -- k1/k2/m1/m2 state change
function onCursorUpdate(x, y, timestamp)
```

---

## Game State (read-only)

| Function | Returns | Description |
|---|---|---|
| `getGameTime()` | float | Current beatmap time in seconds |
| `isGamePlaying()` | bool | Is gameplay active |
| `isPaused()` | bool | Is game paused |
| `getCurrentHp()` | float | Current HP (0-100) |

## Scoring

| Function | Returns | Description |
|---|---|---|
| `getScore()` | int | Total score |
| `getAccuracy()` | float | Accuracy 0.0-1.0 |
| `getCombo()` | int | Current combo |
| `getMaxCombo()` | int | Max possible combo |
| `getObjectsHit()` | int | Objects hit |
| `getMisses()` | int | Miss count |
| `getObjectCount()` | int | Total objects |
| `getUnstableRate()` | float or nil | UR value |
| `getPP()` | float or nil | PP value |

## Difficulty

| Function | Returns | Description |
|---|---|---|
| `getAR()` | float | Approach Rate |
| `getCS()` | float | Circle Size |
| `getOD()` | float | Overall Difficulty |
| `getHP()` | float | Health Drain |
| `getSpeedMultiplier()` | float | Playback speed |

## Engine State

| Function | Returns | Description |
|---|---|---|
| `isKiai()` | bool | Kiai time active |
| `isSliderTracking()` | bool | Currently tracking a slider |
| `isFlashlight()` | bool | Flashlight mod active |
| `isRelax()` | bool | Relax mod active |
| `isAutoplay()` | bool | Autoplay active |
| `getActiveObjectCount()` | int | Objects currently on screen |

## Cursor & Input

| Function | Returns | Description |
|---|---|---|
| `getCursorX()` | float | Cursor X position |
| `getCursorY()` | float | Cursor Y position |
| `isKeyDown(key)` | bool | `key` = "k1","k2","m1","m2" |

## Beatmap Info

| Function | Returns | Description |
|---|---|---|
| `getBeatmapName()` | string | Song title |
| `getBeatmapArtist()` | string | Artist name |
| `getBeatmapDifficulty()` | string | Difficulty name |
| `getActiveMods()` | string | Active mod string |
| `getMods()` | string | Alias for getActiveMods |

## Screen & Playfield

| Function | Returns | Description |
|---|---|---|
| `getScreenSize()` | table | `{width, height}` |
| `getPlayfieldSize()` | table | `{width, height}` |

## Event History

| Function | Returns | Description |
|---|---|---|
| `getHitCount()` | int | Number of recorded hits |
| `getTimingErrors()` | table | Array of timing error values |
| `getRecentHits(count)` | table | Array of recent hit entries |

Each hit entry: `{time, objectId, accuracy, x, y, score, endCombo}`

---

## Text Rendering

All coordinates in game units (virtual resolution, e.g. 512x384).

```lua
handle = createText(text, x, y, sizeSp, {r, g, b, a})
updateText(handle, newText)
moveText(handle, x, y)
setTextColor(handle, {r, g, b, a})
setTextSize(handle, sizeSp)
setTextAlpha(handle, alpha)
setTextVisible(handle, bool)
setTextAlignment(handle, "left"|"center"|"right")
setTextOutline(handle, {r, g, b, a}, thickness)
destroyText(handle)
```

## Sprites

```lua
handle = addSprite(textureName, x, y)
moveSprite(handle, x, y)
scaleSprite(handle, scale)
setSpriteAlpha(handle, alpha)
setSpriteColor(handle, r, g, b, a)
setSpriteRotation(handle, degrees)
setSpriteVisible(handle, bool)
setSpriteZIndex(handle, index)
destroySprite(handle)
```

## Notifications

```lua
showNotification(text, durationSeconds)
```

## Audio

```lua
playSound(soundName)
playCustomSound(soundName, count)
```

---

## Math Utilities

| Function | Description |
|---|---|
| `lerp(a, b, t)` | Linear interpolation |
| `clamp(value, min, max)` | Clamp value to range |
| `distance(x1, y1, x2, y2)` | Distance between two points |
| `angle(x1, y1, x2, y2)` | Angle in degrees |
| `smoothstep(edge0, edge1, x)` | Hermite interpolation |
| `map(value, inMin, inMax, outMin, outMax)` | Remap range |
| `random(a, b)` | Random integer in [a, b] |
| `formatNumber(num, decimals)` | Format to string |

## Table Utilities

| Function | Description |
|---|---|
| `tableSize(t)` | Get table length |
| `tableKeys(t)` | Get array of keys |

---

## Timers

```lua
handle = setTimeout(func, delayMs)
handle = setInterval(func, intervalMs)
clearTimeout(handle)
clearInterval(handle)
```

## File I/O (sandboxed per-plugin)

```lua
ok = fileWrite(filename, content)
content = fileRead(filename)           -- nil if not found
exists = fileExists(filename)
ok = fileDelete(filename)
csv = fileList()                       -- comma-separated names
```

## HTTP

```lua
httpGet(url, function(url, statusCode, body) end)
httpPost(url, body, function(url, statusCode, body) end)
```

---

## Inter-Plugin Communication

```lua
emit(eventName, ...)        -- send event to all plugins
on(eventName, callback)     -- subscribe to event
off(eventName)              -- unsubscribe
```

## Pause/Resume Hooks

```lua
onPause(function()  end)    -- called when game pauses
onResume(function() end)    -- called when game resumes
```

## Utility

```lua
log(message)                -- log to Android logcat
value = getSetting(key)     -- read game preference
```

---

## Plugin Settings

Plugins can be enabled/disabled in: **Settings → Plugins**

Toggle off to skip loading a plugin without deleting its file.

## Example: Minimal Plugin

```lua
name = "My Plugin"
version = "1.0"
author = "Me"
description = "Counts hits"

local hits = 0

function onCircleHit(id, acc, x, y, ec, score)
    if score > 0 then hits = hits + 1 end
end

function onBeatmapFinished(score, combo, acc, grade)
    showNotification("Hits: " .. hits, 3)
    hits = 0
end
```

## Example: Live HUD

```lua
name = "HUD"
version = "1.0"
author = "Me"
description = "Score + Combo overlay"

local scoreText, comboText

function onBeatmapLoaded()
    scoreText = createText("Score: 0", 10, 10, 24, {1,1,1,0.9})
    comboText = createText("Combo: 0", 10, 35, 24, {1,1,0,0.9})
end

function onGameUpdate(dt, time)
    if scoreText then updateText(scoreText, "Score: " .. getScore()) end
    if comboText then updateText(comboText, "Combo: " .. getCombo()) end
end

function onBeatmapFinished(score, combo, acc, grade)
    if scoreText then destroyText(scoreText) end
    if comboText then destroyText(comboText) end
end
```

## Example: Kiai Flash Effect

```lua
name = "Kiai Flash"
version = "1.0"
author = "Me"
description = "Screen flash during kiai"

local flash
local wasKiai = false

function onGameUpdate(dt, time)
    local kiai = isKiai()
    if kiai and not wasKiai then
        showNotification("★ KIAI TIME ★", 1.5)
    end
    wasKiai = kiai
end
```
