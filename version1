//Version 1

<script>
	const shopDomain = "bu1fib-rq.mysho+pify.com";
  const storefrontToken = "83e93e5462232411b85e227dad586a69";
  const apiUrl = https://${shopDomain}/api/2024-10/graphql.json;

  let cart = [];
  
  const cartList = document.getElementById("cart-list");
  const cartCount = document.getElementById("cart-count");
  
  async function shopifyRequest(query, variables = {}) {
    const response = await fetch(apiUrl, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "X-Shopify-Storefront-Access-Token": storefrontToken,
      },
      body: JSON.stringify({ query, variables }),
    });

    const data = await response.json();
    return data;
  }
  
  function showCart(cartList, cartItemsContainer, emptyCartMessage) {
    if (cart.length > 0) {
      if (emptyCartMessage) {
        emptyCartMessage.style.display = "none";
      }

      if (cartItemsContainer) {
        cartItemsContainer.style.display = "block";
        cartItemsContainer.innerHTML = "";

        cart.forEach(item => {
          const listItem = document.createElement("li");
          listItem.textContent = `${item.title} - Cantidad: ${item.quantity} - Precio: $${item.price}`;
          cartItemsContainer.appendChild(listItem);
        });
      }
    } else {
      console.log("Carrito vacío");
    }

    if (cartList) {
      cartList.style.display = "flex";
    } else {
      console.warn("cartList no encontrado en el DOM");
    }
  }

	document.addEventListener("DOMContentLoaded", () => {
    const cartBtn = document.getElementById('cart-button')
    const cartItemsContainer = document.getElementById('cart-items')
    const emptyCartMessage = document.getElementById('empty-cart-message')
    
    cartBtn.addEventListener('click', () => {
			showCart(cartList, cartItemsContainer, emptyCartMessage)
    })
  });
</script>
