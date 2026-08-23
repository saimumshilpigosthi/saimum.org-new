# Next.js 14+ (App Router) Header Implementation Guide

যেহেতু বর্তমান প্রোজেক্টটি শুধুমাত্র HTML/CSS দিয়ে তৈরি একটি UI টেমপ্লেট, তাই এখানে সব পেজে হেডারের কোড কপি-পেস্ট করা আছে। কিন্তু ভবিষ্যতে যখন প্রোজেক্টটি **Next.js 14+**-এ কনভার্ট করা হবে, তখন আমরা DRY (Don't Repeat Yourself) নীতি মেনে অত্যন্ত সুন্দর একটি কম্পোনেন্ট আর্কিটেকচার ফলো করব। 

নিচে ধাপে ধাপে দেখানো হলো কিভাবে Next.js-এ হেডার ইমপ্লিমেন্ট করা হবে:

## ১. ফোল্ডার স্ট্রাকচার
Next.js (App Router) এ আমরা একটি `components` ফোল্ডার রাখব, যেখানে ওয়েবসাইটের কমন অংশগুলো থাকবে।

```text
src/
 ├── app/
 │    ├── layout.jsx        <-- (মাস্টার লেআউট, যেখানে হেডার কল করা হবে)
 │    ├── page.jsx          <-- (হোমপেজ)
 │    ├── about/page.jsx    <-- (এবাউট পেজ)
 │    └── globals.css       <-- (মূল সিএসএস)
 ├── components/
 │    ├── Header.jsx        <-- (হেডার কম্পোনেন্ট)
 │    ├── Footer.jsx        <-- (ফুটার কম্পোনেন্ট)
 │    └── TopBar.jsx        <-- (টপ বার কম্পোনেন্ট)
```

## ২. Header কম্পোনেন্ট তৈরি করা (`src/components/Header.jsx`)

বর্তমানের `index.html` থেকে `<header class="main-header">` এর অংশটুকু নিয়ে আমরা একটি React Component তৈরি করব। 

```jsx
'use client';
import Link from 'next/link';
import { useState } from 'react';
import { usePathname } from 'next/navigation';

export default function Header() {
  const [isMobileMenuOpen, setIsMobileMenuOpen] = useState(false);
  const pathname = usePathname(); // অ্যাকটিভ লিংক হাইলাইট করার জন্য

  return (
    <header className="main-header">
      <div className="container header-container">
        
        {/* Logo */}
        <Link href="/" className="logo">
          <div className="logo-icon"><i className="fa-solid fa-fire-flame-curved"></i></div>
          <div className="logo-text">সাইমুম<span>.org</span></div>
        </Link>
        
        {/* Main Navigation */}
        <nav className={`main-nav ${isMobileMenuOpen ? 'active' : ''}`}>
          <ul>
            <li className="has-dropdown">
              <Link href="#">পরিচিতি</Link>
              <ul className="dropdown">
                <li><Link href="/about">আমাদের সম্পর্কে</Link></li>
                <li><Link href="/founders">প্রতিষ্ঠাতা সদস্যবৃন্দ</Link></li>
              </ul>
            </li>
            
            {/* মোবাইল লগইন বাটন */}
            <li className="mobile-only-login">
              <Link href="/login"><i className="fa-solid fa-user"></i> লগইন</Link>
            </li>
          </ul>

          {/* মোবাইল টপ বার লিংকস */}
          <div className="mobile-top-bar-links">
            <Link href="#"><i className="fa-solid fa-play"></i> সাইমুম স্ট্রিম</Link>
            <Link href="#"><i className="fa-solid fa-graduation-cap"></i> সাইমুম একাডেমি</Link>
          </div>
        </nav>

        {/* Desktop Header Actions */}
        <div className="header-actions">
          <Link href="/login" className="btn btn-primary btn-login">
            <i className="fa-solid fa-user"></i> লগইন
          </Link>
          
          {/* Mobile Menu Toggle Button */}
          <button 
            className="mobile-menu-toggle"
            onClick={() => setIsMobileMenuOpen(!isMobileMenuOpen)}
          >
            <i className="fa-solid fa-bars"></i>
          </button>
        </div>
        
      </div>
    </header>
  );
}
```

## ৩. Root Layout-এ হেডার যুক্ত করা (`src/app/layout.jsx`)

Next.js 14-এর সবচেয়ে বড় সুবিধা হলো এর `layout.jsx` ফাইল। এখানে আমরা একবার `<Header />` কল করে দিলে ওয়েবসাইটের প্রতিটি পেজে অটোমেটিক হেডার শো করবে। কোনো পেজেই আর হেডার কপি-পেস্ট করতে হবে না।

```jsx
import './globals.css';
import Header from '@/components/Header';
import TopBar from '@/components/TopBar';
import Footer from '@/components/Footer';

export const metadata = {
  title: 'সাইমুম শিল্পীগোষ্ঠী - Saimum Shilpigosthi',
  description: 'সুস্থ সংস্কৃতির বিকাশ এবং মানবতার কল্যাণে আমাদের পথচলা',
};

export default function RootLayout({ children }) {
  return (
    <html lang="bn">
      <body>
        <TopBar />
        <Header />
        
        {/* এই children-এর জায়গায় একেক পেজের মেইন কনটেন্ট বসবে */}
        <main>
          {children}
        </main>
        
        <Footer />
      </body>
    </html>
  );
}
```

## Next.js ব্যবহারের সুবিধাসমূহ:
১. **DRY Principle:** একবার মাত্র কোড লিখে পুরো প্রজেক্টে ব্যবহার করা যাবে।
২. **Tailwind CSS Integration:** ভ্যানিলা সিএসএস এর বদলে Tailwind ব্যবহার করলে ক্লাসগুলো আরও সহজে ম্যানেজ করা যাবে।
৩. **React State:** বর্তমানের জাভাস্ক্রিপ্ট (যেমন: মেনু খোলা/বন্ধ করা) React-এর `useState` দিয়ে অনেক সহজভাবে কন্ট্রোল করা যাবে।
৪. **SSR (Server Side Rendering):** নেভিগেশন এবং এসইও (SEO) এর জন্য এটি বেস্ট প্র্যাকটিস।
৫. **<Link> Component:** পেজ রিলোড ছাড়াই খুব দ্রুত এক পেজ থেকে অন্য পেজে যাওয়া যাবে।
