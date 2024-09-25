# Simple javascript message box

### CSS

```css
    /* Overlay that covers the entire screen */
    .mb-overlay {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: rgba(0, 0, 0, 0.5);
      z-index: 9998;
    }
    /* Message window styling */
    .message-box {
      display: flex;
      justify-content: center;
      flex-direction: column;
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 300px;
      padding: 20px;
      border-radius: 10px;
      background-color: #3a3a3a;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
      z-index: 9999;
      text-align: center;
      gap: 10px;
    }
    .message-box .mb-text-container {
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    .message-box .mb-buttons {
      display: flex;
      flex-direction: row;
      gap: 10px;
      justify-content: center;
    }
    .mb-buttons button {
      width: fit-content;
      padding: 10px 20px;
      background-color: #4a4a4a;
      color: #ffffff;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
      border-radius: 10px;
      border: none;
      cursor: pointer;
    }
    .mb-buttons button:hover {
      background-color: #505050;
    }
```

### HTML + Javascript

```html
  <div class="mb-overlay" id="mbOverlay" style="display: none;"></div>
  <div id="mbList"></div>
  <script defer>    
    const mbOverlay = document.getElementById('mbOverlay');
    const mbList = document.getElementById('mbList');

    class MessageBox {
      #message; // String
      #text;    // HTMLElement;
      #buttons; // HTMLElement;
      #box;     // HTMLElement;

      // Helpers
      #show = () => 'flex';
      #hide = () => 'none';

      text(message = '') {
        this.#text.textContent = message;
        return this;
      }

      button(buttonElement, callback = (event) => {}) {
        buttonElement.onclick = () => {
          callback();

          // Hide overlay only if one message box left.
          if (mbList.childElementCount <= 1) {
            mbOverlay.style.display = this.#hide();
          }

          // Message box should be destroyed after handling a button click.
          // This will also remove any onclick handlers on other buttons.
          this.#box.remove();
        };

        this.#buttons.appendChild(buttonElement);
        return this; // For chaining
      }

      #create() {
        // Hide overlay only if one message box left.
        if (mbList.childElementCount < 1) {
          mbOverlay.style.display = this.#show();
        }

        this.#box = document.createElement('div');
        this.#box.className = 'message-box';
        this.#box.style.display = this.#show();
        
        const mbTextContainer = document.createElement('div');
        mbTextContainer.className = 'mb-text-containter';

        this.#text = document.createElement('p');
        this.#text.className = 'mb-text';
        this.#text.textContent = this.#message;
        mbTextContainer.appendChild(this.#text);

        // Store for add() method
        this.#buttons = document.createElement('div');
        this.#buttons.className = 'mb-buttons';

        this.#box.appendChild(mbTextContainer);
        this.#box.appendChild(this.#buttons);
        mbList.appendChild(this.#box);
      }

      constructor(message = '') {
        this.#message = message;
        this.#create();
      }
    }

    // Synchronous message box function
    window.messageBox = function(message = '', buttons = {
      // Default button w/event handler
      'OK': (event) => {}
    }) {
      const mb = new MessageBox(message);

      // Add multiple buttons to message box:
      for (const [label, callback] of Object.entries(buttons)) {
        // Create button element
        const button = document.createElement('button');
        button.textContent = label;

        // Add button and callback to message box
        mb.button(button, callback);
      }
    }
  </script>
```

### Usage

```js
window.messageBox('Hello world!', {'Abort': (event) => alert('Lelouch owns the world! All hail!')});
```
