---
title: KeyboardEvent.code
slug: Web/API/KeyboardEvent/code
---

{{APIRef("UI Events")}}

`KeyboardEvent.code`属性表示键盘上的物理键（与按键生成的字符相对）。换句话说，此属性返回一个值，该值不会被键盘布局或修饰键的状态改变。

如果输入设备不是物理键盘，而是虚拟键盘或辅助功能设备，则浏览器将设置返回值，以尽可能地匹配物理键盘所发生的情况，从而最大限度地提高物理和虚拟输入设备之间的兼容性。

当你想要根据输入设备上的物理位置处理键而不是与这些键相关联的字符时，此属性非常有用;这在编写代码来处理游戏输入时尤为常见，这些游戏使用键盘上的键来模拟类似游戏板的环境。但请注意，你无法使用 `KeyboardEvent.code`报告的值来确定击键生成的字符，因为键码的名称可能与按键上打印的实际字符或按下键时计算机生成的字符不匹配。

例如，QWERTY 布局键盘上的“<kbd>q</kbd>”键返回的`code`是“`KeyQ`”，但 Dvorak 键盘上的“<kbd>'</kbd>”键和 AZERTY 键盘上的“<kbd>a</kbd>”键也返回的相同`code`值。这使得如果用户没有使用预期的键盘布局，则无法使用`code`值来确定用户按键的名称。

要确定哪个字符与键事件对应，请改用{{domxref("KeyboardEvent.key")}}属性。

## 示例

### 练习 KeyboardEvent

#### HTML

```html
<p>
  Press keys on the keyboard to see what the KeyboardEvent's key and code values
  are for each one.
</p>
<div id="output"></div>
```

#### CSS

```css
#output {
  font-family: Arial, Helvetica, sans-serif;
  border: 1px solid black;
}
```

#### JavaScript

```js
window.addEventListener(
  "keydown",
  function (event) {
    let str =
      "KeyboardEvent: key='" + event.key + "' | code='" + event.code + "'";
    let el = document.createElement("span");
    el.innerHTML = str + "<br/>";

    document.getElementById("output").appendChild(el);
  },
  true,
);
```

#### Try it out

To ensure that keystrokes go to the sample, click in the output box below before pressing keys.

{{ EmbedLiveSample('练习 KeyboardEvent', 600, 300) }}

### Handle keyboard events in a game

This example establishes an event listener for [`keydown`](/zh-CN/docs/Web/API/Element/keydown_event) events which handles keyboard input for a game which uses the typical "WASD" keyboard layout for steering forward, left, backward, and right. This will use the same four keys physically regardless of what the actual corresponding characters are, such as if the user is using an AZERTY keyboard.

#### HTML

```html
<p>Use the WASD (ZQSD on AZERTY) keys to move and steer.</p>
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" class="world">
  <polygon id="spaceship" points="15,0 0,30 30,30" />
</svg>
<script>
  refresh();
</script>
```

#### CSS

```css
.world {
  margin: 0px;
  padding: 0px;
  background-color: black;
  width: 400px;
  height: 400px;
}

#spaceship {
  fill: orange;
  stroke: red;
  stroke-width: 2px;
}
```

#### JavaScript

The first section of the JavaScript code establishes some variables we'll be using. `shipSize` contains the size of the ship the player is moving around, for convenience. `position` is used to track the position of the ship within the play field. `moveRate` and `turnRate` are the number of pixels forward and backward each keystroke moves the ship and how many degrees of rotation the left and right steering controls apply per keystroke. angle is the current amount of rotation applied to the ship, in degrees; it starts at 0° (pointing straight up). Finally, `spaceship` is set to refer to the element with the ID `"spaceship"`, which is the SVG polygon representing the ship the player controls.

```js
let shipSize = {
  width: 30,
  height: 30,
};

let position = {
  x: 200,
  y: 200,
};

let moveRate = 9;
let turnRate = 5;

let angle = 0;

let spaceship = document.getElementById("spaceship");
```

Next comes the function `updatePosition()`. This function takes as input the distance the ship is to be moved, where positive is a forward movement and negative is a backward movement. This function computes the new position of the ship given the distance moved and the current direction the ship is facing. It also handles ensuring that the ship wraps across the boundaries of the play field instead of vanishing.

```js
function updatePosition(offset) {
  let rad = angle * (Math.PI / 180);
  position.x += Math.sin(rad) * offset;
  position.y -= Math.cos(rad) * offset;

  if (position.x < 0) {
    position.x = 399;
  } else if (position.x > 399) {
    position.x = 0;
  }

  if (position.y < 0) {
    position.y = 399;
  } else if (position.y > 399) {
    position.y = 0;
  }
}
```

The `refresh()` function handles applying the rotation and position by using an [SVG transform](/zh-CN/docs/Web/SVG/Reference/Attribute/transform).

```js
function refresh() {
  let x = position.x - shipSize.width / 2;
  let y = position.y - shipSize.height / 2;
  let transform = "translate(" + x + " " + y + ") rotate(" + angle + " 15 15) ";

  spaceship.setAttribute("transform", transform);
}
```

Finally, the `addEventListener()` method is used to start listening for [`keydown`](/zh-CN/docs/Web/API/Element/keydown_event) events, acting on each key by updating the ship position and rotation angle, then calling `refresh()` to draw the ship at its new position and angle.

```js
window.addEventListener(
  "keydown",
  function (event) {
    if (event.preventDefaulted) {
      return; // Do nothing if event already handled
    }

    switch (event.code) {
      case "KeyS":
      case "ArrowDown":
        // Handle "back"
        updatePosition(-moveRate);
        break;
      case "KeyW":
      case "ArrowUp":
        // Handle "forward"
        updatePosition(moveRate);
        break;
      case "KeyA":
      case "ArrowLeft":
        // Handle "turn left"
        angle -= turnRate;
        break;
      case "KeyD":
      case "ArrowRight":
        // Handle "turn right"
        angle += turnRate;
        break;
    }

    refresh();

    // Consume the event so it doesn't get handled twice
    event.preventDefault();
  },
  true,
);
```

#### Try it out

To ensure that keystrokes go to the sample code, click inside the black game play field below before pressing keys.

{{EmbedLiveSample("Handle_keyboard_events_in_a_game", 420, 460)}}

There are several ways this code can be made better. Most real games would watch for [`keydown`](/zh-CN/docs/Web/API/Element/keydown_event) events, start motion when that happens, and stop the motion when the corresponding [`keyup`](/zh-CN/docs/Web/API/Element/keyup_event) occurs, instead of relying on key repeats. That would allow both smoother and faster movement, but would also allow the player to be moving and steering at the same time. Transitions or animations could be used to make the ship's movement smoother, too.

## Specification

{{Specifications}}

## Browser compatibility

{{Compat}}

## Code values

The following tables show what code values are used for each native scancode or virtual keycode on major platforms. Because some browsers choose to interpret physical keys differently, there are some differences in which keys map to which codes. These tables show those variations when known.

### Code values on Windows

This table shows the Windows scan codes representing keys and the `KeyboardEvent.code` values which correspond to those hardware keys. Only keys which generate scan codes on Windows are listed.

<table>
  <thead>
    <tr>
      <th scope="row"></th>
      <th colspan="2" rowspan="1" scope="col" style="text-align: center">
        <strong><code>KeyboardEvent.code</code></strong> value
      </th>
    </tr>
    <tr>
      <th scope="row">Code</th>
      <th scope="col">Firefox</th>
      <th scope="col">Chrome</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row"><code>0x0000</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0001</code></th>
      <td><code>"Escape"</code></td>
      <td><code>"Escape"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0002</code></th>
      <td><code>"Digit0"</code></td>
      <td><code>"Digit0"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0003</code></th>
      <td><code>"Digit1"</code></td>
      <td><code>"Digit1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0004</code></th>
      <td><code>"Digit2"</code></td>
      <td><code>"Digit2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0005</code></th>
      <td><code>"Digit3"</code></td>
      <td><code>"Digit3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0006</code></th>
      <td><code>"Digit4"</code></td>
      <td><code>"Digit4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0007</code></th>
      <td><code>"Digit5"</code></td>
      <td><code>"Digit5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0008</code></th>
      <td><code>"Digit6"</code></td>
      <td><code>"Digit6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0009</code></th>
      <td><code>"Digit7"</code></td>
      <td><code>"Digit7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000A</code></th>
      <td><code>"Digit8"</code></td>
      <td><code>"Digit8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000B</code></th>
      <td><code>"Digit9"</code></td>
      <td><code>"Digit9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000C</code></th>
      <td><code>"Minus"</code></td>
      <td><code>"Minus"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000D</code></th>
      <td><code>"Equal"</code></td>
      <td><code>"Equal"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000E</code></th>
      <td><code>"Backspace"</code></td>
      <td><code>"Backspace"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000F</code></th>
      <td><code>"Tab"</code></td>
      <td><code>"Tab"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0010</code></th>
      <td><code>"KeyQ"</code></td>
      <td><code>"KeyQ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0011</code></th>
      <td><code>"KeyW"</code></td>
      <td><code>"KeyW"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0012</code></th>
      <td><code>"KeyE"</code></td>
      <td><code>"KeyE"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0013</code></th>
      <td><code>"KeyR"</code></td>
      <td><code>"KeyR"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0014</code></th>
      <td><code>"KeyT"</code></td>
      <td><code>"KeyT"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0015</code></th>
      <td><code>"KeyY"</code></td>
      <td><code>"KeyY"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0016</code></th>
      <td><code>"KeyU"</code></td>
      <td><code>"KeyU"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0017</code></th>
      <td><code>"KeyI"</code></td>
      <td><code>"KeyI"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0018</code></th>
      <td><code>"KeyO"</code></td>
      <td><code>"KeyO"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0019</code></th>
      <td><code>"KeyP"</code></td>
      <td><code>"KeyP"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001A</code></th>
      <td><code>"BracketLeft"</code></td>
      <td><code>"BracketLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001B</code></th>
      <td><code>"BracketRight"</code></td>
      <td><code>"BracketRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001C</code></th>
      <td><code>"Enter"</code></td>
      <td><code>"Enter"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001D</code></th>
      <td><code>"ControlLeft"</code></td>
      <td><code>"ControlLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001E</code></th>
      <td><code>"KeyA"</code></td>
      <td><code>"KeyA"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001F</code></th>
      <td><code>"KeyS"</code></td>
      <td><code>"KeyS"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0020</code></th>
      <td><code>"KeyD"</code></td>
      <td><code>"KeyD"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0021</code></th>
      <td><code>"KeyF"</code></td>
      <td><code>"KeyF"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0022</code></th>
      <td><code>"KeyG"</code></td>
      <td><code>"KeyG"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0023</code></th>
      <td><code>"KeyH"</code></td>
      <td><code>"KeyH"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0024</code></th>
      <td><code>"KeyJ"</code></td>
      <td><code>"KeyJ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0025</code></th>
      <td><code>"KeyK"</code></td>
      <td><code>"KeyK"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0026</code></th>
      <td><code>"KeyL"</code></td>
      <td><code>"KeyL"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0027</code></th>
      <td><code>"Semicolon"</code></td>
      <td><code>"Semicolon"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0028</code></th>
      <td><code>"Quote"</code></td>
      <td><code>"Quote"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0029</code></th>
      <td><code>"Backquote"</code></td>
      <td><code>"Backquote"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002A</code></th>
      <td><code>"ShiftLeft"</code></td>
      <td><code>"ShiftLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002B</code></th>
      <td><code>"Backslash"</code></td>
      <td><code>"Backslash"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002C</code></th>
      <td><code>"KeyZ"</code></td>
      <td><code>"KeyZ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002D</code></th>
      <td><code>"KeyX"</code></td>
      <td><code>"KeyX"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002E</code></th>
      <td><code>"KeyC"</code></td>
      <td><code>"KeyC"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002F</code></th>
      <td><code>"KeyV"</code></td>
      <td><code>"KeyV"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0030</code></th>
      <td><code>"KeyB"</code></td>
      <td><code>"KeyB"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0031</code></th>
      <td><code>"KeyN"</code></td>
      <td><code>"KeyN"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0032</code></th>
      <td><code>"KeyM"</code></td>
      <td><code>"KeyM"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0033</code></th>
      <td><code>"Comma"</code></td>
      <td><code>"Comma"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0034</code></th>
      <td><code>"Period"</code></td>
      <td><code>"Period"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0035</code></th>
      <td><code>"Slash"</code></td>
      <td><code>"Slash"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0036</code></th>
      <td><code>"ShiftRight"</code></td>
      <td><code>"ShiftRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0037</code></th>
      <td><code>"NumpadMultiply"</code></td>
      <td><code>"NumpadMultiply"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0038</code></th>
      <td><code>"AltLeft"</code></td>
      <td><code>"AltLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0039</code></th>
      <td><code>"Space"</code></td>
      <td><code>"Space"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003A</code></th>
      <td><code>"CapsLock"</code></td>
      <td><code>"CapsLock"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003B</code></th>
      <td><code>"F1"</code></td>
      <td><code>"F1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003C</code></th>
      <td><code>"F2"</code></td>
      <td><code>"F2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003D</code></th>
      <td><code>"F3"</code></td>
      <td><code>"F3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003E</code></th>
      <td><code>"F4"</code></td>
      <td><code>"F4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003F</code></th>
      <td><code>"F5"</code></td>
      <td><code>"F5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0040</code></th>
      <td><code>"F6"</code></td>
      <td><code>"F6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0041</code></th>
      <td><code>"F7"</code></td>
      <td><code>"F7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0042</code></th>
      <td><code>"F8"</code></td>
      <td><code>"F8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0043</code></th>
      <td><code>"F9"</code></td>
      <td><code>"F9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0044</code></th>
      <td><code>"F10"</code></td>
      <td><code>"F10"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0045</code></th>
      <td><code>"Pause"</code></td>
      <td><code>"Pause"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0046</code></th>
      <td><code>"ScrollLock"</code></td>
      <td><code>"ScrollLock"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0047</code></th>
      <td><code>"Numpad7"</code></td>
      <td><code>"Numpad7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0048</code></th>
      <td><code>"Numpad8"</code></td>
      <td><code>"Numpad8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0049</code></th>
      <td><code>"Numpad9"</code></td>
      <td><code>"Numpad9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004A</code></th>
      <td><code>"NumpadSubtract"</code></td>
      <td><code>"NumpadSubtract"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004B</code></th>
      <td><code>"Numpad4"</code></td>
      <td><code>"Numpad4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004C</code></th>
      <td><code>"Numpad5"</code></td>
      <td><code>"Numpad5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004D</code></th>
      <td><code>"Numpad6"</code></td>
      <td><code>"Numpad6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004E</code></th>
      <td><code>"NumpadAdd"</code></td>
      <td><code>"NumpadAdd"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004F</code></th>
      <td><code>"Numpad1"</code></td>
      <td><code>"Numpad1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0050</code></th>
      <td><code>"Numpad2"</code></td>
      <td><code>"Numpad2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0051</code></th>
      <td><code>"Numpad3"</code></td>
      <td><code>"Numpad3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0052</code></th>
      <td><code>"Numpad0"</code></td>
      <td><code>"Numpad0"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0053</code></th>
      <td><code>"NumpadDecimal"</code></td>
      <td><code>"NumpadDecimal"</code></td>
    </tr>
    <tr>
      <th scope="row">
        <code>0x0054 (<kbd>Alt</kbd> + <kbd>PrintScreen</kbd>)</code>
      </th>
      <td><code>"PrintScreen"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0055</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0056</code></th>
      <td><code>"IntlBackslash"</code></td>
      <td><code>"IntlBackslash"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0057</code></th>
      <td><code>"F11"</code></td>
      <td><code>"F11"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0058</code></th>
      <td><code>"F12"</code></td>
      <td><code>"F12"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0059</code></th>
      <td><code>"NumpadEqual"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005A</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005B</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F13"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005C</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F14"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005D</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F15"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005E</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005F</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0060</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0061</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0062</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0063</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F16"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0064</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F13"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F17"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0065</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F14"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F18"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0066</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F15"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F19"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0067</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F16"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F20"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0068</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F17"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F21"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0069</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F18"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F22"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006A</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F19"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F23"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006B</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F20"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>"F24"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006C</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F21"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006D</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F22"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006E</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F23"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006F</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0070</code></th>
      <td><code>"KanaMode"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row">
        <code>0x0071</code> (<kbd>Hanja</kbd> key without Korean keyboard
        layout)
      </th>
      <td><code>"Lang2"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row">
        <code>0x0072</code> (<kbd>Han/Yeong</kbd> key without Korean keyboard
        layout)
      </th>
      <td><code>"Lang1"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0073</code></th>
      <td><code>"IntlRo"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0074</code>, <code>0x0075</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0076</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F24"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0077</code>, <code>0x0078</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0079</code></th>
      <td><code>"Convert"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007A</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007B</code></th>
      <td><code>"NonConvert"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007C</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007D</code></th>
      <td><code>"IntlYen"</code></td>
      <td><code>"IntlYen"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007E</code></th>
      <td><code>"NumpadComma"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007F</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE000</code> ～ <code>0xE007</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE008</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>"Undo"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE009</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE00A</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>"Paste"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE00B</code> ～ <code>0xE00F</code></th>
      <td>""</td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE010</code></th>
      <td><code>"MediaTrackPrevious"</code></td>
      <td><code>"MediaTrackPrevious"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE011</code> ～ <code>0xE016</code></th>
      <td><code>""</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE017</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>"Cut"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE018</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>"Copy"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE019</code></th>
      <td><code>"MediaTrackNext"</code></td>
      <td><code>"MediaTrackNext"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE01A, 0xE01B</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE01C</code></th>
      <td><code>"NumpadEnter"</code></td>
      <td><code>"NumpadEnter"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE01D</code></th>
      <td><code>"ControlRight"</code></td>
      <td><code>"ControlRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE01E</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"LaunchMail"</code>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0xE01F</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE020</code></th>
      <td>
        <code>"AudioVolumeMute"</code> (was <code>"VolumeMute"</code> until
        Firefox 49)
      </td>
      <td>
        <code>"AudioVolumeMute"</code> (was <code>"VolumeMute"</code> until
        Chrome 50)
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0xE021</code></th>
      <td><code>"LaunchApp2"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE022</code></th>
      <td><code>"MediaPlayPause"</code></td>
      <td><code>"MediaPlayPause"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE023</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE024</code></th>
      <td><code>"MediaStop"</code></td>
      <td><code>"MediaStop"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE025</code> ～ <code>0xE02B</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE02C</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>"Eject"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE02D</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE02E</code></th>
      <td>
        <p><code>"AudioVolumeDown"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>"VolumeDown"</code>.
          </p>
        </div>
      </td>
      <td>
        <code>"VolumeDown"</code> (was <code>"VolumeDown"</code> until Chrome
        50)
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0xE02F</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE030</code></th>
      <td>
        <p><code>"AudioVolumeUp"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>"VolumeUp"</code>.
          </p>
        </div>
      </td>
      <td>
        <code>"VolumeUp"</code> (was <code>"VolumeUp"</code> until Chrome 50)
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0xE031</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE032</code></th>
      <td><code>"BrowserHome"</code></td>
      <td><code>"BrowserHome"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE033</code>, <code>0xE034</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE035</code></th>
      <td><code>"NumpadDivide"</code></td>
      <td><code>"NumpadDivide"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE036</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE037</code></th>
      <td><code>"PrintScreen"</code></td>
      <td><code>"PrintScreen"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE038</code></th>
      <td><code>"AltRight"</code></td>
      <td><code>"AltRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE039</code>, <code>0xE03A</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE03B</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>"Help"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE03C</code>～ <code>0xE044</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE045</code></th>
      <td><code>"NumLock"</code></td>
      <td><code>"NumLock"</code></td>
    </tr>
    <tr>
      <th scope="row">
        <code>0xE046</code> (<kbd>Ctrl</kbd> + <kbd>Pause</kbd>)
      </th>
      <td><code>"Pause"</code></td>
      <td><code>"Pause"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE047</code></th>
      <td><code>"Home"</code></td>
      <td><code>"Home"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE048</code></th>
      <td><code>"ArrowUp"</code></td>
      <td><code>"ArrowUp"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE049</code></th>
      <td><code>"PageUp"</code></td>
      <td><code>"PageUp"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE04A</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE04B</code></th>
      <td><code>"ArrowLeft"</code></td>
      <td><code>"ArrowLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE04C</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE04D</code></th>
      <td><code>"ArrowRight"</code></td>
      <td><code>"ArrowRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE04E</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE04F</code></th>
      <td><code>"End"</code></td>
      <td><code>"End"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE050</code></th>
      <td><code>"ArrowDown"</code></td>
      <td><code>"ArrowDown"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE051</code></th>
      <td><code>"PageDown"</code></td>
      <td><code>"PageDown"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE052</code></th>
      <td><code>"Insert"</code></td>
      <td><code>"Insert"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE053</code></th>
      <td><code>"Delete"</code></td>
      <td><code>"Delete"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE054</code> ～ <code>0xE05A</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE05B</code></th>
      <td>
        <p><code>"MetaLeft"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code was reported as
            <code>"OSLeft"</code>.
          </p>
        </div>
      </td>
      <td><code>"OSLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE05C</code></th>
      <td>
        <p><code>"MetaRight"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code was reported as
            <code>"OSRight"</code>.
          </p>
        </div>
      </td>
      <td><code>"OSRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE05D</code></th>
      <td><code>"ContextMenu"</code></td>
      <td><code>"ContextMenu"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE05E</code></th>
      <td><code>"Power"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE05F</code> ～ <code>0xE064</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE065</code></th>
      <td><code>"BrowserSearch"</code></td>
      <td><code>"BrowserSearch"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE066</code></th>
      <td><code>"BrowserFavorites"</code></td>
      <td><code>"BrowserFavorites"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE067</code></th>
      <td><code>"BrowserRefresh"</code></td>
      <td><code>"BrowserRefresh"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE068</code></th>
      <td><code>"BrowserStop"</code></td>
      <td><code>"BrowserStop"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE069</code></th>
      <td><code>"BrowserForward"</code></td>
      <td><code>"BrowserForward"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE06A</code></th>
      <td><code>"BrowserBack"</code></td>
      <td><code>"BrowserBack"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE06B</code></th>
      <td><code>"LaunchApp1"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE06C</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"LaunchMail"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE06D</code></th>
      <td>
        <code>"LaunchMediaPlayer"</code> (<code>"MediaSelect"</code> prior to
        Firefox 49)
      </td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0xE06E ～ 0xE0F0</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row">
        <code>0xE0F1</code> (<kbd>Hanja</kbd> key with Korean keyboard layout)
      </th>
      <td><code>"Lang2"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row">
        <code>0xE0F2</code> (<kbd>Han/Yeong</kbd> key with Korean keyboard
        layout)
      </th>
      <td><code>"Lang1"</code></td>
      <td style="background-color: rgb(220, 199, 255)"><code>""</code></td>
    </tr>
  </tbody>
</table>

### Code values on Mac

On Mac OS X, it's hard to get scancode or something which can distinguish a physical key from a key event. Therefore, Gecko always maps `code` value from the virtual keycode.

<table>
  <thead>
    <tr>
      <th scope="row">Virtual keycode</th>
      <th colspan="1" rowspan="1" scope="col">Gecko (32)</th>
      <th rowspan="1" scope="col">Chromium (48)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row"><code>kVK_ANSI_A (0x00)</code></th>
      <td><code>"KeyA"</code></td>
      <td><code>"KeyA"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_S (0x01)</code></th>
      <td><code>"KeyS"</code></td>
      <td><code>"KeyS"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_D (0x02)</code></th>
      <td><code>"KeyD"</code></td>
      <td><code>"KeyD"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_F (0x03)</code></th>
      <td><code>"KeyF"</code></td>
      <td><code>"KeyF"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_H (0x04)</code></th>
      <td><code>"KeyH"</code></td>
      <td><code>"KeyH"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_G (0x05)</code></th>
      <td><code>"KeyG"</code></td>
      <td><code>"KeyG"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Z (0x06)</code></th>
      <td><code>"KeyZ"</code></td>
      <td><code>"KeyZ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_X (0x07)</code></th>
      <td><code>"KeyX"</code></td>
      <td><code>"KeyX"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_C (0x08)</code></th>
      <td><code>"KeyC"</code></td>
      <td><code>"KeyC"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_V (0x09)</code></th>
      <td><code>"KeyV"</code></td>
      <td><code>"KeyV"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ISO_Section (0x0A)</code></th>
      <td><code>"IntlBackslash"</code></td>
      <td><code>"IntlBackslash"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_B (0x0B)</code></th>
      <td><code>"KeyB"</code></td>
      <td><code>"KeyB"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Q (0x0C)</code></th>
      <td><code>"KeyQ"</code></td>
      <td><code>"KeyQ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_W (0x0D)</code></th>
      <td><code>"KeyW"</code></td>
      <td><code>"KeyW"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_E (0x0E)</code></th>
      <td><code>"KeyE"</code></td>
      <td><code>"KeyE"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_R (0x0F)</code></th>
      <td><code>"KeyR"</code></td>
      <td><code>"KeyR"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Y (0x10)</code></th>
      <td><code>"KeyY"</code></td>
      <td><code>"KeyY"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_T (0x11)</code></th>
      <td><code>"KeyT"</code></td>
      <td><code>"KeyT"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_1 (0x12)</code></th>
      <td><code>"Digit1"</code></td>
      <td><code>"Digit1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_2 (0x13)</code></th>
      <td><code>"Digit2"</code></td>
      <td><code>"Digit2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_3 (0x14)</code></th>
      <td><code>"Digit3"</code></td>
      <td><code>"Digit3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_4 (0x15)</code></th>
      <td><code>"Digit4"</code></td>
      <td><code>"Digit4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_6 (0x16)</code></th>
      <td><code>"Digit6"</code></td>
      <td><code>"Digit6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_5 (0x17)</code></th>
      <td><code>"Digit7"</code></td>
      <td><code>"Digit7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Equal (0x18)</code></th>
      <td><code>"Equal"</code></td>
      <td><code>"Equal"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_9 (0x19)</code></th>
      <td><code>"Digit9"</code></td>
      <td><code>"Digit9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_7 (0x1A)</code></th>
      <td><code>"Digit7"</code></td>
      <td><code>"Digit7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Minus (0x1B)</code></th>
      <td><code>"Minus"</code></td>
      <td><code>"Minus"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_8 (0x1C)</code></th>
      <td><code>"Digit8"</code></td>
      <td><code>"Digit8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_0 (0x1D)</code></th>
      <td><code>"Digit0"</code></td>
      <td><code>"Digit0"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_RightBracket (0x1E)</code></th>
      <td><code>"BracketRight"</code></td>
      <td><code>"BracketRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_O (0x1F)</code></th>
      <td><code>"KeyO"</code></td>
      <td><code>"KeyO"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_U (0x20)</code></th>
      <td><code>"KeyU"</code></td>
      <td><code>"KeyU"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_LeftBracket (0x21)</code></th>
      <td><code>"BracketLeft"</code></td>
      <td><code>"BracketLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_I (0x22)</code></th>
      <td><code>"KeyI"</code></td>
      <td><code>"KeyI"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_P (0x23)</code></th>
      <td><code>"KeyP"</code></td>
      <td><code>"KeyP"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Return (0x24)</code></th>
      <td><code>"Enter"</code></td>
      <td><code>"Enter"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_L (0x25)</code></th>
      <td><code>"KeyL"</code></td>
      <td><code>"KeyL"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_J (0x26)</code></th>
      <td><code>"KeyJ"</code></td>
      <td><code>"KeyJ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Quote (0x27)</code></th>
      <td><code>"Quote"</code></td>
      <td><code>"Quote"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_K (0x28)</code></th>
      <td><code>"KeyK"</code></td>
      <td><code>"KeyK"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Semicolon (0x29)</code></th>
      <td><code>"Semicolon"</code></td>
      <td><code>"Semicolon"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Tab (0x30)</code></th>
      <td><code>"Tab"</code></td>
      <td><code>"Tab"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Space (0x31)</code></th>
      <td><code>"Space"</code></td>
      <td><code>"Space"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Grave (0x32)</code></th>
      <td><code>"Backquote"</code></td>
      <td><code>"Backquote"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Delete (0x33)</code></th>
      <td><code>"Backspace"</code></td>
      <td><code>"Backspace"</code></td>
    </tr>
    <tr>
      <th scope="row">Enter key on keypad of PowerBook (<code>0x34</code>)</th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"NumpadEnter"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Escape (0x35)</code></th>
      <td><code>"Escape"</code></td>
      <td><code>"Escape"</code></td>
    </tr>
    <tr>
      <th scope="row">right-command key (<code>0x36</code>)</th>
      <td><code>"OSRight"</code></td>
      <td><code>"OSRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Command (0x37)</code></th>
      <td><code>"OSLeft"</code></td>
      <td><code>"OSLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Shift (0x38)</code></th>
      <td><code>"ShiftLeft"</code></td>
      <td><code>"ShiftLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_CapsLock (0x39)</code></th>
      <td><code>"CapsLock"</code></td>
      <td><code>"CapsLock"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Option (0x3A)</code></th>
      <td><code>"AltLeft"</code></td>
      <td><code>"AltLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Control (0x3B)</code></th>
      <td><code>"ControlLeft"</code></td>
      <td><code>"ControlLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_RightShift (0x3C)</code></th>
      <td><code>"ShiftRight"</code></td>
      <td><code>"ShiftRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_RightOption (0x3D)</code></th>
      <td><code>"AltRight"</code></td>
      <td><code>"AltRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_RightControl (0x3E)</code></th>
      <td><code>"ControlRight"</code></td>
      <td><code>"ControlRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Function (0x3F)</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"Fn"</code><em> (no events fired actually)</em>
      </td>
      <td style="background-color: rgb(255, 255, 204)">
        <code>""</code><em> (no events fired actually)</em>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F17 (0x40)</code></th>
      <td><code>"F17"</code></td>
      <td><code>"F17"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_KeypadDecimal (0x41)</code></th>
      <td><code>"NumpadDecimal"</code></td>
      <td><code>"NumpadDecimal"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_KeypadMultiply (0x43)</code></th>
      <td><code>"NumpadMultiply"</code></td>
      <td><code>"NumpadMultiply"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_KeypadPlus (0x45)</code></th>
      <td><code>"NumpadAdd"</code></td>
      <td><code>"NumpadAdd"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_KeypadClear (0x47)</code></th>
      <td><code>"NumLock"</code></td>
      <td><code>"NumLock"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_VolumeUp (0x48)</code></th>
      <td>
        <code>"AudioVolumeUp" </code>(was <code>"VolumeUp"</code> until Firefox
        48)
      </td>
      <td>
        <code>"AudioVolumeUp" </code>(was <code>"VolumeUp"</code> until Chrome
        50)
      </td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_VolumeDown (0x49)</code></th>
      <td>
        <code>"AudioVolumeDown"</code> (was <code>"VolumeDown"</code> until
        Firefox 49)
      </td>
      <td>
        <code>"AudioVolumeDown"</code> (was <code>"VolumeDown"</code> until
        Chrome 50)
      </td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Mute (0x4A)</code></th>
      <td>
        <code>"AudioVolumeMute"</code> (was <code>"VolumeMute"</code> until
        Firefox 49)
      </td>
      <td>
        <code>"AudioVolumeMute"</code> (was <code>"VolumeMute"</code> until
        Chrome 50)
      </td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_KeypadDivide (0x4B)</code></th>
      <td><code>"NumpadDivide"</code></td>
      <td><code>"NumpadDivide"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_KeypadEnter (0x4C)</code></th>
      <td><code>"NumpadEnter"</code></td>
      <td><code>"NumpadEnter"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_KeypadMinus (0x4E)</code></th>
      <td><code>"NumpadSubtract"</code></td>
      <td><code>"NumpadSubtract"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F18 (0x4F)</code></th>
      <td><code>"F18"</code></td>
      <td><code>"F18"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F19 (0x50)</code></th>
      <td><code>"F19"</code></td>
      <td><code>"F19"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_KeypadEquals (0x51)</code></th>
      <td><code>"NumpadEqual"</code></td>
      <td><code>"NumpadEqual"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Keypad0 (0x52)</code></th>
      <td><code>"Numpad0"</code></td>
      <td><code>"Numpad0"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Keypad1 (0x53)</code></th>
      <td><code>"Numpad1"</code></td>
      <td><code>"Numpad1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Keypad2 (0x54)</code></th>
      <td><code>"Numpad2"</code></td>
      <td><code>"Numpad2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Keypad3 (0x55)</code></th>
      <td><code>"Numpad3"</code></td>
      <td><code>"Numpad3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Keypad4 (0x56)</code></th>
      <td><code>"Numpad4"</code></td>
      <td><code>"Numpad4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Keypad5 (0x57)</code></th>
      <td><code>"Numpad5"</code></td>
      <td><code>"Numpad5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Keypad6 (0x58)</code></th>
      <td><code>"Numpad6"</code></td>
      <td><code>"Numpad6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Keypad7 (0x59)</code></th>
      <td><code>"Numpad7"</code></td>
      <td><code>"Numpad7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F20 (0x5A)</code></th>
      <td><code>"F20"</code></td>
      <td><code>"F20"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Keypad8 (0x5B)</code></th>
      <td><code>"Numpad8"</code></td>
      <td><code>"Numpad8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ANSI_Keypad9 (0x5C)</code></th>
      <td><code>"Numpad9"</code></td>
      <td><code>"Numpad9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_JIS_Yen (0x5D)</code></th>
      <td><code>"IntlYen"</code></td>
      <td><code>"IntlYen"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_JIS_Underscore (0x5E)</code></th>
      <td><code>"IntlRo"</code></td>
      <td><code>"IntlRo"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_JIS_KeypadComma (0x5F)</code></th>
      <td><code>"NumpadComma"</code></td>
      <td><code>"NumpadComma"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F5 (0x60)</code></th>
      <td><code>"F5"</code></td>
      <td><code>"F5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F6 (0x61)</code></th>
      <td><code>"F6"</code></td>
      <td><code>"F6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F7 (0x62)</code></th>
      <td><code>"F7"</code></td>
      <td><code>"F7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F3 (0x63)</code></th>
      <td><code>"F3"</code></td>
      <td><code>"F3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F8 (0x64)</code></th>
      <td><code>"F8"</code></td>
      <td><code>"F8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F9 (0x65)</code></th>
      <td><code>"F9"</code></td>
      <td><code>"F9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_JIS_Eisu (0x66)</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <p><code>"Lang2"</code></p>
        <div class="note">
          <p>
            Prior to
            <a href="/zh-CN/docs/Mozilla/Firefox/Releases/37">Firefox 37</a>,
            this key incorrectly generated the key code
            <code>"RomanCharacters"</code>.
          </p>
        </div>
      </td>
      <td style="background-color: rgb(255, 255, 204)">
        <code>""</code><em> (no events fired actually)</em>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F11 (0x67)</code></th>
      <td><code>"F11"</code></td>
      <td><code>"F11"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_JIS_Kana (0x68)</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"Lang1"</code></td>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"KanaMode"</code><em> (no events fired actually)</em>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F13 (0x69)</code></th>
      <td><code>"F13"</code></td>
      <td><code>"F13"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F16 (0x6A)</code></th>
      <td><code>"F16"</code></td>
      <td><code>"F16"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F14 (0x6B)</code></th>
      <td><code>"F14"</code></td>
      <td><code>"F14"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F10 (0x6D)</code></th>
      <td><code>"F10"</code></td>
      <td><code>"F10"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F12 (0x6F)</code></th>
      <td><code>"F12"</code></td>
      <td><code>"F12"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F15 (0x71)</code></th>
      <td><code>"F15"</code></td>
      <td><code>"F15"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Help (0x72)</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"Help"</code></td>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"Insert"</code>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_Home (0x73)</code></th>
      <td><code>"Home"</code></td>
      <td><code>"Home"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_PageUp (0x74)</code></th>
      <td><code>"PageUp"</code></td>
      <td><code>"PageUp"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_ForwardDelete (0x75)</code></th>
      <td><code>"Delete"</code></td>
      <td><code>"Delete"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F4 (0x76)</code></th>
      <td><code>"F4"</code></td>
      <td><code>"F4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_End (0x77)</code></th>
      <td><code>"End"</code></td>
      <td><code>"End"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F2 (0x78)</code></th>
      <td><code>"F2"</code></td>
      <td><code>"F2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_PageDown (0x79)</code></th>
      <td><code>"PageDown"</code></td>
      <td><code>"PageDown"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_F1 (0x7A)</code></th>
      <td><code>"F1"</code></td>
      <td><code>"F1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_LeftArrow (0x7B)</code></th>
      <td><code>"ArrowLeft"</code></td>
      <td><code>"ArrowLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_RightArrow (0x7C)</code></th>
      <td><code>"ArrowRight"</code></td>
      <td><code>"ArrowRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_DownArrow (0x7D)</code></th>
      <td><code>"ArrowDown"</code></td>
      <td><code>"ArrowDown"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>kVK_UpArrow (0x7E)</code></th>
      <td><code>"ArrowUp"</code></td>
      <td><code>"ArrowUp"</code></td>
    </tr>
  </tbody>
</table>

### Code values on Linux (X11) (When scancode is available)

Note that X has too many keys and some of them are not testable with usual keyboard. So, following table is created from source code which maps from scancode to code value.

<table>
  <thead>
    <tr>
      <th scope="row">scancode (hardware_keycode)</th>
      <th scope="col">Gecko (32)</th>
      <th scope="col">Chromium (44)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row"><code>0x0009</code></th>
      <td><code>"Escape"</code></td>
      <td><code>"Escape"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000A</code></th>
      <td><code>"Digit1"</code></td>
      <td><code>"Digit1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000B</code></th>
      <td><code>"Digit2"</code></td>
      <td><code>"Digit2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000C</code></th>
      <td><code>"Digit3"</code></td>
      <td><code>"Digit3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000D</code></th>
      <td><code>"Digit4"</code></td>
      <td><code>"Digit4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000E</code></th>
      <td><code>"Digit5"</code></td>
      <td><code>"Digit5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000F</code></th>
      <td><code>"Digit6"</code></td>
      <td><code>"Digit6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0010</code></th>
      <td><code>"Digit7"</code></td>
      <td><code>"Digit7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0011</code></th>
      <td><code>"Digit8"</code></td>
      <td><code>"Digit8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0012</code></th>
      <td><code>"Digit9"</code></td>
      <td><code>"Digit9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0013</code></th>
      <td><code>"Digit0"</code></td>
      <td><code>"Digit0"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0014</code></th>
      <td><code>"Minus"</code></td>
      <td><code>"Minus"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0015</code></th>
      <td><code>"Equal"</code></td>
      <td><code>"Equal"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0016</code></th>
      <td><code>"Backspace"</code></td>
      <td><code>"Backspace"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0017</code></th>
      <td><code>"Tab"</code></td>
      <td><code>"Tab"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0018</code></th>
      <td><code>"KeyQ"</code></td>
      <td><code>"KeyQ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0019</code></th>
      <td><code>"KeyW"</code></td>
      <td><code>"KeyW"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001A</code></th>
      <td><code>"KeyE"</code></td>
      <td><code>"KeyE"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001B</code></th>
      <td><code>"KeyR"</code></td>
      <td><code>"KeyR"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001C</code></th>
      <td><code>"KeyT"</code></td>
      <td><code>"KeyT"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001D</code></th>
      <td><code>"KeyY"</code></td>
      <td><code>"KeyY"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001E</code></th>
      <td><code>"KeyU"</code></td>
      <td><code>"KeyU"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001F</code></th>
      <td><code>"KeyI"</code></td>
      <td><code>"KeyI"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0020</code></th>
      <td><code>"KeyO"</code></td>
      <td><code>"KeyO"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0021</code></th>
      <td><code>"KeyP"</code></td>
      <td><code>"KeyP"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0022</code></th>
      <td><code>"BracketLeft"</code></td>
      <td><code>"BracketLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0023</code></th>
      <td><code>"BracketRight"</code></td>
      <td><code>"BracketRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0024</code></th>
      <td><code>"Enter"</code></td>
      <td><code>"Enter"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0025</code></th>
      <td><code>"ControlLeft"</code></td>
      <td><code>"ControlLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0026</code></th>
      <td><code>"KeyA"</code></td>
      <td><code>"KeyA"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0027</code></th>
      <td><code>"KeyS"</code></td>
      <td><code>"KeyS"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0028</code></th>
      <td><code>"KeyD"</code></td>
      <td><code>"KeyD"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0029</code></th>
      <td><code>"KeyF"</code></td>
      <td><code>"KeyF"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002A</code></th>
      <td><code>"KeyG"</code></td>
      <td><code>"KeyG"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002B</code></th>
      <td><code>"KeyH"</code></td>
      <td><code>"KeyH"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002C</code></th>
      <td><code>"KeyJ"</code></td>
      <td><code>"KeyJ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002D</code></th>
      <td><code>"KeyK"</code></td>
      <td><code>"KeyK"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002E</code></th>
      <td><code>"KeyL"</code></td>
      <td><code>"KeyL"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002F</code></th>
      <td><code>"Semicolon"</code></td>
      <td><code>"Semicolon"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0030</code></th>
      <td><code>"Quote"</code></td>
      <td><code>"Quote"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0031</code></th>
      <td><code>"Backquote"</code></td>
      <td><code>"Backquote"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0032</code></th>
      <td><code>"ShiftLeft"</code></td>
      <td><code>"ShiftLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0033</code></th>
      <td><code>"Backslash"</code></td>
      <td><code>"Backslash"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0034</code></th>
      <td><code>"KeyZ"</code></td>
      <td><code>"KeyZ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0035</code></th>
      <td><code>"KeyX"</code></td>
      <td><code>"KeyX"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0036</code></th>
      <td><code>"KeyC"</code></td>
      <td><code>"KeyC"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0037</code></th>
      <td><code>"KeyV"</code></td>
      <td><code>"KeyV"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0038</code></th>
      <td><code>"KeyB"</code></td>
      <td><code>"KeyB"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0039</code></th>
      <td><code>"KeyN"</code></td>
      <td><code>"KeyN"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003A</code></th>
      <td><code>"KeyM"</code></td>
      <td><code>"KeyM"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003B</code></th>
      <td><code>"Comma"</code></td>
      <td><code>"Comma"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003C</code></th>
      <td><code>"Period"</code></td>
      <td><code>"Period"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003D</code></th>
      <td><code>"Slash"</code></td>
      <td><code>"Slash"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003E</code></th>
      <td><code>"ShiftRight"</code></td>
      <td><code>"ShiftRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003F</code></th>
      <td><code>"NumpadMultiply"</code></td>
      <td><code>"NumpadMultiply"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0040</code></th>
      <td><code>"AltLeft"</code></td>
      <td><code>"AltLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0041</code></th>
      <td><code>"Space"</code></td>
      <td><code>"Space"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0042</code></th>
      <td><code>"CapsLock"</code></td>
      <td><code>"CapsLock"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0043</code></th>
      <td><code>"F1"</code></td>
      <td><code>"F1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0044</code></th>
      <td><code>"F2"</code></td>
      <td><code>"F2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0045</code></th>
      <td><code>"F3"</code></td>
      <td><code>"F3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0046</code></th>
      <td><code>"F4"</code></td>
      <td><code>"F4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0047</code></th>
      <td><code>"F5"</code></td>
      <td><code>"F5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0048</code></th>
      <td><code>"F6"</code></td>
      <td><code>"F6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0049</code></th>
      <td><code>"F7"</code></td>
      <td><code>"F7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004A</code></th>
      <td><code>"F8"</code></td>
      <td><code>"F8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004B</code></th>
      <td><code>"F9"</code></td>
      <td><code>"F9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004C</code></th>
      <td><code>"F10"</code></td>
      <td><code>"F10"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004D</code></th>
      <td><code>"NumLock"</code></td>
      <td><code>"NumLock"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004E</code></th>
      <td><code>"ScrollLock"</code></td>
      <td><code>"ScrollLock"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004F</code></th>
      <td><code>"Numpad7"</code></td>
      <td><code>"Numpad7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0050</code></th>
      <td><code>"Numpad8"</code></td>
      <td><code>"Numpad8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0051</code></th>
      <td><code>"Numpad9"</code></td>
      <td><code>"Numpad9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0052</code></th>
      <td><code>"NumpadSubtract"</code></td>
      <td><code>"NumpadSubtract"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0053</code></th>
      <td><code>"Numpad4"</code></td>
      <td><code>"Numpad4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0054</code></th>
      <td><code>"Numpad5"</code></td>
      <td><code>"Numpad5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0055</code></th>
      <td><code>"Numpad6"</code></td>
      <td><code>"Numpad6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0056</code></th>
      <td><code>"NumpadAdd"</code></td>
      <td><code>"NumpadAdd"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0057</code></th>
      <td><code>"Numpad1"</code></td>
      <td><code>"Numpad1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0058</code></th>
      <td><code>"Numpad2"</code></td>
      <td><code>"Numpad2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0059</code></th>
      <td><code>"Numpad3"</code></td>
      <td><code>"Numpad3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005A</code></th>
      <td><code>"Numpad0"</code></td>
      <td><code>"Numpad0"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005B</code></th>
      <td><code>"NumpadDecimal"</code></td>
      <td><code>"NumpadDecimal"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005C</code>, <code>0x005D</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005E</code></th>
      <td><code>"IntlBackslash"</code></td>
      <td><code>"IntlBackslash"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005F</code></th>
      <td><code>"F11"</code></td>
      <td><code>"F11"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0060</code></th>
      <td><code>"F12"</code></td>
      <td><code>"F12"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0061</code></th>
      <td><code>"IntlRo"</code></td>
      <td><code>"IntlRo"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0062</code>, <code>0x0063</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0064</code></th>
      <td><code>"Convert"</code></td>
      <td><code>"Convert"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0065</code></th>
      <td><code>"KanaMode"</code></td>
      <td><code>"KanaMode"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0066</code></th>
      <td><code>"NonConvert"</code></td>
      <td><code>"NonConvert"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0067</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0068</code></th>
      <td><code>"NumpadEnter"</code></td>
      <td><code>"NumpadEnter"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0069</code></th>
      <td><code>"ControlRight"</code></td>
      <td><code>"ControlRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006A</code></th>
      <td><code>"NumpadDivide"</code></td>
      <td><code>"NumpadDivide"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006B</code></th>
      <td><code>"PrintScreen"</code></td>
      <td><code>"PrintScreen"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006C</code></th>
      <td><code>"AltRight"</code></td>
      <td><code>"AltRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006D</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006E</code></th>
      <td><code>"Home"</code></td>
      <td><code>"Home"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006F</code></th>
      <td><code>"ArrowUp"</code></td>
      <td><code>"ArrowUp"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0070</code></th>
      <td><code>"PageUp"</code></td>
      <td><code>"PageUp"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0071</code></th>
      <td><code>"ArrowLeft"</code></td>
      <td><code>"ArrowLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0072</code></th>
      <td><code>"ArrowRight"</code></td>
      <td><code>"ArrowRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0073</code></th>
      <td><code>"End"</code></td>
      <td><code>"End"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0074</code></th>
      <td><code>"ArrowDown"</code></td>
      <td><code>"ArrowDown"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0075</code></th>
      <td><code>"PageDown"</code></td>
      <td><code>"PageDown"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0076</code></th>
      <td><code>"Insert"</code></td>
      <td><code>"Insert"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0077</code></th>
      <td><code>"Delete"</code></td>
      <td><code>"Delete"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0078</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0079</code></th>
      <td>
        <code>"AudioVolumeMute"</code> (was <code>"VolumeMute"</code> until
        Firefox 49)
      </td>
      <td>
        <code>"AudioVolumeMute"</code> (was <code>"VolumeMute"</code> until
        Chrome 50)
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x007A</code></th>
      <td>
        <code>"AudioVolumeDown"</code> (was <code>"VolumeDown"</code> until
        Firefox 49)
      </td>
      <td>
        <code>"AudioVolumeDown"</code> (was <code>"VolumeDown"</code> until
        Chrome 50)
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x007B</code></th>
      <td>
        <code>"AudioVolumeUp"</code> (was <code>"VolumeUp"</code> until Firefox
        49)
      </td>
      <td>
        <code>"AudioVolumeUp"</code> (was <code>"VolumeUp"</code> until Chrome
        50)
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x007C</code></th>
      <td style="background-color: rgb(204, 255, 255)">
        <code>"Unidentified"</code>
      </td>
      <td style="background-color: rgb(204, 255, 255)"><code>"Power"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007D</code></th>
      <td><code>"NumpadEqual"</code></td>
      <td><code>"NumpadEqual"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007E</code></th>
      <td style="background-color: rgb(204, 255, 255)">
        <code>"Unidentified"</code>
      </td>
      <td style="background-color: rgb(204, 255, 255)">
        <code>"NumpadChangeSign"</code>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x007F</code></th>
      <td><code>"Pause"</code></td>
      <td><code>"Pause"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0080</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0081</code></th>
      <td><code>"NumpadComma"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0082</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"Lang1"</code></td>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"HangulMode"</code>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0083</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"Lang2"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>"Hanja"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0084</code></th>
      <td><code>"IntlYen"</code></td>
      <td><code>"IntlYen"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0085</code></th>
      <td><code>"OSLeft"</code></td>
      <td><code>"OSLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0086</code></th>
      <td><code>"OSRight"</code></td>
      <td><code>"OSRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0087</code></th>
      <td><code>"ContextMenu"</code></td>
      <td><code>"ContextMenu"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0088</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"BrowserStop"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"Cancel"</code>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0089</code></th>
      <td style="background-color: rgb(255, 255, 204)">"Again"</td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x008A</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"Props"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x008B</code></th>
      <td><code>"Undo"</code></td>
      <td><code>"Undo"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x008C</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"Select"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x008D</code></th>
      <td><code>"Copy"</code></td>
      <td><code>"Copy"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x008E</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"Open"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x008F</code></th>
      <td><code>"Paste"</code></td>
      <td><code>"Paste"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0090</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"Find"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0091</code></th>
      <td><code>"Cut"</code></td>
      <td><code>"Cut"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0092</code></th>
      <td><code>"Help"</code></td>
      <td><code>"Help"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0093</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0094</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"LaunchApp2"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0095</code>, <code>0x0096</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0097</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"WakeUp"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0098</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"LaunchApp1"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0099</code> ～ <code>0x00A2</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A3</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"LaunchMail"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A4</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"BrowserFavorites"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A5</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A6</code></th>
      <td><code>"BrowserBack"</code></td>
      <td><code>"BrowserBack"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A7</code></th>
      <td><code>"BrowserForward"</code></td>
      <td><code>"BrowserForward"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A8</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A9</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"Eject"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00AA</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00AB</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"MediaTrackNext"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00AC</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"MediaPlayPause"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00AD</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"MediaTrackPrevious"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00AE</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"MediaStop"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00AF</code> ～ <code>0x00B2</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00B3</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"LaunchMediaPlayer"</code> (<code>"MediaSelect"</code> prior to
        Firefox 49)
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00B4</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"BrowserHome"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00B5</code></th>
      <td><code>"BrowserRefresh"</code></td>
      <td><code>"BrowserRefresh"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00B6</code> ～ <code>0x00BA</code></th>
      <td><code>"Unidentified"</code></td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00BB</code></th>
      <td style="background-color: rgb(204, 255, 255)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(204, 255, 255)">
        <code>"NumpadParenLeft"</code>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x00BC</code></th>
      <td style="background-color: rgb(204, 255, 255)">
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td style="background-color: rgb(204, 255, 255)">
        <code>"NumpadParenRight"</code>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x00BD</code>, <code>0x00BE</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00BF</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F13"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C0</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F14"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C1</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F15"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C2</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F16"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C3</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F17"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C4</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F18"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C5</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F19"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C6</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F20"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C7</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F21"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C8</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F22"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C9</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F23"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00CA</code></th>
      <td style="background-color: rgb(255, 255, 204)"><code>"F24"</code></td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00CB ～ 0x00E0</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
      <td><code>""</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00E1</code></th>
      <td style="background-color: rgb(255, 255, 204)">
        <code>"BrowserSearch"</code>
      </td>
      <td style="background-color: rgb(255, 255, 204)"><code>""</code></td>
    </tr>
  </tbody>
</table>

### Code values on Android and Firefox OS (When scancode is available)

<table>
  <thead>
    <tr>
      <th scope="row">scancode</th>
      <th scope="col">Gecko (32)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row"><code>0x0001</code></th>
      <td><code>"Escape"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0002</code></th>
      <td><code>"Digit1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0003</code></th>
      <td><code>"Digit2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0004</code></th>
      <td><code>"Digit3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0005</code></th>
      <td><code>"Digit4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0006</code></th>
      <td><code>"Digit5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0007</code></th>
      <td><code>"Digit6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0008</code></th>
      <td><code>"Digit7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0009</code></th>
      <td><code>"Digit8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000A</code></th>
      <td><code>"Digit9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000B</code></th>
      <td><code>"Digit0"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000C</code></th>
      <td><code>"Minus"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000D</code></th>
      <td><code>"Equal"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000E</code></th>
      <td><code>"Backspace"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x000F</code></th>
      <td><code>"Tab"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0010</code></th>
      <td><code>"KeyQ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0011</code></th>
      <td><code>"KeyW"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0012</code></th>
      <td><code>"KeyE"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0013</code></th>
      <td><code>"KeyR"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0014</code></th>
      <td><code>"KeyT"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0015</code></th>
      <td><code>"KeyY"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0016</code></th>
      <td><code>"KeyU"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0017</code></th>
      <td><code>"KeyI"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0018</code></th>
      <td><code>"KeyO"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0019</code></th>
      <td><code>"KeyP"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001A</code></th>
      <td><code>"BracketLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001B</code></th>
      <td><code>"BracketRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001C</code></th>
      <td><code>"Enter"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001D</code></th>
      <td><code>"ControlLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001E</code></th>
      <td><code>"KeyA"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x001F</code></th>
      <td><code>"KeyS"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0020</code></th>
      <td><code>"KeyD"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0021</code></th>
      <td><code>"KeyF"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0022</code></th>
      <td><code>"KeyG"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0023</code></th>
      <td><code>"KeyH"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0024</code></th>
      <td><code>"KeyJ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0025</code></th>
      <td><code>"KeyK"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0026</code></th>
      <td><code>"KeyL"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0027</code></th>
      <td><code>"Semicolon"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0028</code></th>
      <td><code>"Quote"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0029</code></th>
      <td><code>"Backquote"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002A</code></th>
      <td><code>"ShiftLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002B</code></th>
      <td><code>"Backslash"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002C</code></th>
      <td><code>"KeyZ"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002D</code></th>
      <td><code>"KeyX"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002E</code></th>
      <td><code>"KeyC"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x002F</code></th>
      <td><code>"KeyV"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0030</code></th>
      <td><code>"KeyB"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0031</code></th>
      <td><code>"KeyN"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0032</code></th>
      <td><code>"KeyM"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0033</code></th>
      <td><code>"Comma"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0034</code></th>
      <td><code>"Period"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0035</code></th>
      <td><code>"Slash"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0036</code></th>
      <td><code>"ShiftRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0037</code></th>
      <td><code>"NumpadMultiply"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0038</code></th>
      <td><code>"AltLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0039</code></th>
      <td><code>"Space"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003A</code></th>
      <td><code>"CapsLock"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003B</code></th>
      <td><code>"F1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003C</code></th>
      <td><code>"F2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003D</code></th>
      <td><code>"F3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003E</code></th>
      <td><code>"F4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x003F</code></th>
      <td><code>"F5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0040</code></th>
      <td><code>"F6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0041</code></th>
      <td><code>"F7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0042</code></th>
      <td><code>"F8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0043</code></th>
      <td><code>"F9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0044</code></th>
      <td><code>"F10"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0045</code></th>
      <td><code>"NumLock"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0046</code></th>
      <td><code>"ScrollLock"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0047</code></th>
      <td><code>"Numpad7"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0048</code></th>
      <td><code>"Numpad8"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0049</code></th>
      <td><code>"Numpad9"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004A</code></th>
      <td><code>"NumpadSubtract"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004B</code></th>
      <td><code>"Numpad4"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004C</code></th>
      <td><code>"Numpad5"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004D</code></th>
      <td><code>"Numpad6"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004E</code></th>
      <td><code>"NumpadAdd"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x004F</code></th>
      <td><code>"Numpad1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0050</code></th>
      <td><code>"Numpad2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0051</code></th>
      <td><code>"Numpad3"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0052</code></th>
      <td><code>"Numpad0"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0053</code></th>
      <td><code>"NumpadDecimal"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0054</code>, <code>0x0055</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0056</code></th>
      <td><code>"IntlBackslash"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0057</code></th>
      <td><code>"F11"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0058</code></th>
      <td><code>"F12"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0059</code></th>
      <td><code>"IntlRo"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005A</code>, <code>0x005B</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x005C</code></th>
      <td><code>"Convert"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005D</code></th>
      <td><code>"KanaMode"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005E</code></th>
      <td><code>"NonConvert"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x005F</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0060</code></th>
      <td><code>"NumpadEnter"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0061</code></th>
      <td><code>"ControlRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0062</code></th>
      <td><code>"NumpadDivide"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0063</code></th>
      <td><code>"PrintScreen"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0064</code></th>
      <td><code>"AltRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0065</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0066</code></th>
      <td><code>"Home"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0067</code></th>
      <td><code>"ArrowUp"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0068</code></th>
      <td><code>"PageUp"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0069</code></th>
      <td><code>"ArrowLeft"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006A</code></th>
      <td><code>"ArrowRight"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006B</code></th>
      <td><code>"End"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006C</code></th>
      <td><code>"ArrowDown"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006D</code></th>
      <td><code>"PageDown"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006E</code></th>
      <td><code>"Insert"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x006F</code></th>
      <td><code>"Delete"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0070</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0071</code></th>
      <td>
        <p><code>"AudioVolumeMute"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is identifi9ed as
            <code>"VolumeMute"</code>.
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0072</code></th>
      <td>
        <p><code>"AudioVolumeDown"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is identifi9ed as
            <code>"VolumeDown"</code>.
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0073</code></th>
      <td>
        <p><code>"AudioVolumeUp"</code></p>
        <p>
          Prior to Firefox 48, this key code is identifi9ed as
          <code>"VolumeUp"</code>.
        </p>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0074</code></th>
      <td><code>"Power"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0075</code></th>
      <td><code>"NumpadEqual"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0076</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0077</code></th>
      <td><code>"Pause"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0078</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x0079</code></th>
      <td><code>"NumpadComma"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007A</code></th>
      <td><code>"Lang1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007B</code></th>
      <td><code>"Lang2"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007C</code></th>
      <td><code>"IntlYen"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x007D</code></th>
      <td>
        <p><code>"MetaLeft"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>"OSLeft"</code>.
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x007E</code></th>
      <td>
        <p><code>"MetaRight"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 49, this key code is reported as
            <code>"MetaRight"</code>.
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x007F</code></th>
      <td><code>"ContextMenu"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0080</code></th>
      <td><code>"BrowserStop"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0081</code></th>
      <td>"Again"</td>
    </tr>
    <tr>
      <th scope="row"><code>0x0082</code></th>
      <td><code>"Props"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0083</code></th>
      <td><code>"Undo"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0084</code></th>
      <td><code>"Select"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0085</code></th>
      <td><code>"Copy"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0086</code></th>
      <td><code>"Open"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0087</code></th>
      <td><code>"Paste"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0088</code></th>
      <td><code>"Find"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0089</code></th>
      <td><code>"Cut"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x008A</code></th>
      <td><code>"Help"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x008B</code> ～ <code>0x008D</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x008E</code></th>
      <td><code>"Sleep"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x008F</code></th>
      <td><code>"WakeUp"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0090</code></th>
      <td><code>"LaunchApp1"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x0091</code> ～ <code>0x009B</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x009C</code></th>
      <td><code>"BrowserFavorites"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x009D</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x009E</code></th>
      <td><code>"BrowserBack"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x009F</code></th>
      <td><code>"BrowserForward"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A0</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A1</code></th>
      <td><code>"Eject"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A2</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A3</code></th>
      <td><code>"MediaTrackNext"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A4</code></th>
      <td><code>"MediaPlayPause"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A5</code></th>
      <td><code>"MediaTrackPrevious"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A6</code></th>
      <td><code>"MediaStop"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00A7</code> ～ <code>0x00AC</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x00AD</code></th>
      <td><code>"BrowserRefresh"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00AE</code> ～ <code>0x00B6</code></th>
      <td>
        "Unidentified" Prior to Firefox 48, this key code is reported as "" (an
        empty string).
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x00B7</code></th>
      <td><code>"F13"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00B8</code></th>
      <td><code>"F14"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00B9</code></th>
      <td><code>"F15"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00BA</code></th>
      <td><code>"F16"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00BB</code></th>
      <td><code>"F17"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00BC</code></th>
      <td><code>"F18"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00BD</code></th>
      <td><code>"F19"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00BE</code></th>
      <td><code>"F20"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00BF</code></th>
      <td><code>"F21"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C0</code></th>
      <td><code>"F22"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C1</code></th>
      <td><code>"F23"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C2</code></th>
      <td><code>"F24"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00C3</code> ～ <code>0x00D8</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x00D9</code></th>
      <td><code>"BrowserSearch"</code></td>
    </tr>
    <tr>
      <th scope="row"><code>0x00DA</code> ～ <code>0x01CF</code></th>
      <td>
        <p><code>"Unidentified"</code></p>
        <div class="note">
          <p>
            Prior to Firefox 48, this key code is reported as
            <code>""</code> (an empty string).
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <th scope="row"><code>0x01D0</code></th>
      <td><code>"Fn"</code></td>
    </tr>
  </tbody>
</table>
