const shopDomain = import.meta.env.VITE_SHOPIFY_DOMAIN;
const storefrontToken = import.meta.env.VITE_SHOPIFY_STOREFRONT_TOKEN;

const apiUrl = `https://${shopDomain}/api/2024-10/graphql.json`;

let cart = [];

const cartCountEl = document.getElementById("cart-count");
const cartItemsEl = document.getElementById("cart-items");

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

function updateCartDisplay() {
  localStorage.setItem("cart", JSON.stringify(cart));

  cartCountEl.textContent = cart.length;
  
  const total = cart.reduce((sum, item) => sum + item.price, 0);
  
  cartItemsEl.innerHTML = "";
  
  cart.forEach((item, index) => {
    const li = document.createElement("li");
    li.innerHTML = `
      ${index + 1}. ${item.title} 
      <strong>$${item.price.toFixed(2)} ${item.currency}</strong>
    `;
    cartItemsEl.appendChild(li);
  });
  
  if (cart.length > 0) {
    const totalLi = document.createElement("li");
    totalLi.className = "cart-total";
    totalLi.innerHTML = `<strong>Total: $${total.toFixed(2)} ${cart[0]?.currency || 'USD'}</strong>`;
    cartItemsEl.appendChild(totalLi);
  }
}

function addToCart(productData) {  
  const item = {
    title: productData.title,
    price: parseFloat(productData.priceRange.minVariantPrice.amount),
    currency: productData.priceRange.minVariantPrice.currencyCode,
    variantId: productData.variants.edges[0].node.id
  };
  cart.push(item);
  updateCartDisplay();
}

async function createShopifyCheckout() {
  if (cart.length === 0) {
    alert("Tu carrito está vacío");
    return;
  }

  const groupedItems = cart.reduce((acc, item) => {
    const existing = acc.find(i => i.merchandiseId === item.variantId);
    if (existing) {
      existing.quantity += 1;
    } else {
      acc.push({
        merchandiseId: item.variantId,
        quantity: 1
      });
    }
    return acc;
  }, []);

  const cartQuery = `
    mutation cartCreate($input: CartInput!) {
      cartCreate(input: $input) {
        cart {
          id
          checkoutUrl
          totalQuantity
          cost {
            totalAmount {
              amount
              currencyCode
            }
          }
        }
        userErrors {
          field
          message
          code
        }
      }
    }
  `;

  const variables = {
    input: {
      lines: groupedItems 
    }
  };

  try {
    const cartData = await shopifyRequest(cartQuery, variables);
    
    if (cartData.errors && cartData.errors.length > 0) {
      alert(`Error de GraphQL: ${cartData.errors[0].message}`);
      return;
    }
    
    if (!cartData.data || !cartData.data.cartCreate) {
      alert("Error: Respuesta inválida de Shopify");
      return;
    }
    
    const { cart: shopifyCart, userErrors } = cartData.data.cartCreate;
    
    if (userErrors && userErrors.length > 0) {
      alert(`Error: ${userErrors[0].message} (Código: ${userErrors[0].code})`);
      return;
    }

    if (!shopifyCart || !shopifyCart.checkoutUrl) {
      alert("Error: No se pudo crear el carrito");
      return;
    }
    
    cart = [];
    updateCartDisplay();
    
    window.location.href = shopifyCart.checkoutUrl;
    
  } catch (error) {
    alert(`Error de conexión: ${error.message}`);
  }
}

document.getElementById("add-to-cart-btn").addEventListener("click", async () => {
  const query = `
  {
    products(first: 1) {
      edges {
        node {
          title
          variants(first: 1) {
            edges {
              node {
                id
                price {
                  amount
                  currencyCode
                }
                title
              }
            }
          }
        }
      }
    }
  }
  `;

  const data = await shopifyRequest(query);
  const product = data.data.products.edges[0].node;
  const variant = product.variants.edges[0].node;

  const productData = {
    title: product.title,
    priceRange: {
      minVariantPrice: {
        amount: variant.price.amount,     
        currencyCode: variant.price.currencyCode
      }
    },
    variants: {
      edges: [{
        node: {
          id: variant.id
        }
      }]
    }
  };

  addToCart(productData);
});

document.getElementById("checkout-btn").addEventListener("click", async () => {
  await createShopifyCheckout();
});

document.getElementById("cart-icon").addEventListener("click", () => {
  const cartBox = document.getElementById("cart-box");
  cartBox.style.display = cartBox.style.display === "none" ? "block" : "none";
});
