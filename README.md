import { useState } from "react";
import { motion } from "framer-motion";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { ShoppingCart } from "lucide-react";
import { loadStripe } from "@stripe/stripe-js";

const stripePromise = loadStripe("YOUR_STRIPE_PUBLISHABLE_KEY");

const products = [
  {
    id: 1,
    name: "Apex R1 Reflex Red Dot Optic",
    price: 139.99,
    image: "https://i.postimg.cc/PxVBYpYw/optics4.jpg", 
    description: "Compact aluminum reflex red dot optic with adjustable brightness and wide viewing window. Designed for sporting and target optics applications.",
  },
  {
    id: 2,
    name: "Apex R2 Micro Red Dot Optic",
    price: 119.99,
    image: "https://i.postimg.cc/PxVBYpYw/optics4.jpg",
    description: "Ultra-compact micro red dot optic built for lightweight setups and fast visual clarity. Minimal profile with adjustable brightness for sporting use.",
  },
];

export default function DropshippingStore() {
  const [cart, setCart] = useState([]);

  const addToCart = (product) => {
    setCart([...cart, product]);
  };

  const handleCheckout = async () => {
    const stripe = await stripePromise;

    await stripe.redirectToCheckout({
      lineItems: cart.map((item) => ({
        price_data: {
          currency: "usd",
          product_data: {
            name: item.name,
            description: item.description,
            images: [item.image],
          },
          unit_amount: Math.round(item.price * 100),
        },
        quantity: 1,
      })),
      mode: "payment",
      successUrl: window.location.origin + "/success",
      cancelUrl: window.location.origin,
    });
  };

  return (
    <div className="min-h-screen bg-gray-50 p-6">
      <header className="flex items-center justify-between mb-16">
        <h1 className="text-2xl font-semibold tracking-tight">Apex Optics</h1>
        <div className="flex items-center gap-2 text-gray-700">
          <ShoppingCart className="h-5 w-5" />
          <span className="text-sm">{cart.length}</span>
        </div>
      </header>

      <div className="mb-10 rounded-lg border border-gray-200 bg-white p-4 text-xs text-gray-600">
        We do not sell firearms, ammunition, or controlled weapon parts.
      </div>

      <h2 className="text-xl font-medium mb-2">Apex R1 Reflex Red Dot Optic</h2>
      <p className="text-gray-600 mb-8 max-w-2xl">
        A clean, lightweight reflex red dot optic engineered for fast target acquisition and clarity. Built from aluminum alloy with a low-profile design for everyday sporting and range use.
      </p>

      <motion.div
        className="grid grid-cols-1 gap-8 max-w-4xl"
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
      >
        {products.map((product) => (
          <Card key={product.id} className="rounded-2xl shadow-sm">
            <img
              src={product.image}
              alt={product.name}
              className="h-80 w-full object-cover rounded-t-2xl"
            />
            <CardContent className="p-6 space-y-4">
              <div>
                <h3 className="text-lg font-semibold">{product.name}</h3>
                <p className="text-gray-600 mt-1">{product.description}</p>
              </div>

              <ul className="text-sm text-gray-700 list-disc pl-5 space-y-1">
                <li>Aluminum alloy housing</li>
                <li>Adjustable red dot brightness</li>
                <li>Wide viewing window for fast acquisition</li>
                <li>Lightweight, low-profile design</li>
                <li>Optics-only accessory (no controlled components)</li>
              </ul>

              <div className="flex items-center justify-between pt-4">
                <span className="text-lg font-medium">${product.price}</span>
                <Button onClick={() => addToCart(product)}>
                  Add to Cart
                </Button>
              </div>
            </CardContent>
          </Card>
        ))}
      </motion.div>

      <section className="mt-20 max-w-xl">
        <h2 className="text-xl font-medium mb-3">Secure Checkout</h2>
        <p className="text-gray-600 mb-2">
          All payments are securely processed via Stripe. We sell optics and accessories only.
        </p>
        <p className="text-gray-600 mb-4">
          We do not sell firearms, ammunition, or controlled weapon parts.
        </p>
        <Button size="lg" className="mt-2" onClick={handleCheckout}>Proceed to Secure Payment</Button>
      </section>

      <section className="mt-24 max-w-3xl space-y-12 text-sm text-gray-700">
        <div>
          <h3 className="text-xl font-semibold mb-2">Refund Policy</h3>
          <p>
            We offer a 30-day return policy. If you are not satisfied with your purchase,
            you may return the item within 30 days of delivery for a full refund.
            Items must be unused and in original packaging.
          </p>
        </div>

        <div>
          <h3 className="text-xl font-semibold mb-2">Shipping Policy</h3>
          <p>
            Orders are processed within 1–3 business days. Shipping typically takes
            5–10 business days depending on your location. Tracking information
            will be provided once your order ships.
          </p>
        </div>

        <div>
          <h3 className="text-xl font-semibold mb-2">Privacy Policy</h3>
          <p>
            We respect your privacy. Personal information collected during checkout
            is used solely to process your order and provide customer support.
            We do not sell or share your information with third parties.
          </p>
        </div>

        <div>
          <h3 className="text-xl font-semibold mb-2">Terms of Service</h3>
          <p>
            By using this website, you agree to our terms and conditions. Apex Optics
            sells optics and accessories only and complies with all applicable laws.
            Customers are responsible for ensuring products are legal in their
            jurisdiction.
          </p>
        </div>
      </section>

      <footer className="mt-20 text-sm text-gray-500 space-y-2">
        <p>We do not sell firearms, ammunition, or controlled weapon parts.</p>
        <p>© {new Date().getFullYear()} Apex Optics. All rights reserved.</p>
      </footer>
    </div>
  );
}
