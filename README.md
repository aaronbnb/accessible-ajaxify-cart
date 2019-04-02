# accessible-ajaxify-cart
AjaxifyModal with accessibility features, setting focus and trapping the tab key

## Key Additions



```javascript
// Private general variables
  var settings, cartInit, $drawerHeight, $cssTransforms, $cssTransforms3d, $nojQueryLoad, $w, $body, $html, $focusableElementsString, $focusableElements, $firstTabStop, $lastTabStop, $previouslyFocused;
```

```javascript
// On modal init
    // find all focusable children, for use within the modal
    $focusableElementsString = 'a[href], area[href], input:not([disabled]), select:not([disabled]), ' + 
    'textarea:not([disabled]), button:not([disabled]), iframe, object, embed, [tabindex="0"], [contenteditable]';
    
// save a reference to the current element in focus
    $previouslyFocused = document.activeElement ? document.activeElement : $body;

```

```javascript
  showModal = function (toggle) {
    $body.addClass('ajaxcart--is-visible');
    // Build the cart if it isn't already there
    if (!cartInit && toggle) {
      Shopify.getCart(cartToggleCallback);
    } else {
      sizeModal();
    }
  };
  
  returnFocus = function () {
    // when modal closes, return focus to the element that initiated opening the modal
    setTimeout(function() {
      $previouslyFocused.focus();
    }, 500);
  }

  // manage focus within modal
  manageFocus = function() {
    // save current focus, in other words, the element that initiated opening the modal   
    $previouslyFocused = document.activeElement;
    
    // Find all focusable children in the modal
    $focusableElements = $modalContainer.find($focusableElementsString);
    
    $firstTabStop = $focusableElements[0];
    $lastTabStop = $focusableElements[$focusableElements.length - 1];
    
    // Listen for and trap the keyboard
    $modalContainer.on('keydown', trapTabKey);
    
    // Set focus to first element in modal.
    $firstTabStop.focus();
  }
  
  // Listen for and trap the keyboard in the modal
  trapTabKey = function(e) {
     if (e.keyCode === 9) {
      // SHIFT + TAB
      if (e.shiftKey) {
        if (document.activeElement === $firstTabStop) {
          e.preventDefault();
          $lastTabStop.focus();
        }
      // TAB
      } else {
        if (document.activeElement === $lastTabStop) {
          e.preventDefault();
          $firstTabStop.focus();
        }
      }
    }
  }

  sizeModal = function(isResizing) {
    
    if (!isResizing) {
      $modalContainer.css('opacity', 0);
    }

    // Position modal by negative margin
    $modalContainer.css({
      'margin-left': - ($modalContainer.outerWidth() / 2),
      'opacity': 1
    });

    // Position close button relative to title
    $closeCart.css({
      'top': 30 + ($cartContainer.find('h1').height() / 2)
    });

    $modalContainer.addClass('is-visible');

    scrollTop();

    toggleCallback({
      'is_visible': true
    });

    manageFocus();
  };

  hideModal = function (e) {
    $body.removeClass('ajaxcart--is-visible');
    if (e) {
      e.preventDefault();
    }

    if ($modalContainer) {
      $modalContainer.removeClass('is-visible');
      $body.removeClass('ajaxify-lock');
    }

    toggleCallback({
      'is_visible': false
    });
    
    returnFocus();
  };

```
