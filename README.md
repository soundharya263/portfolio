<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1" />
    <meta name="description" content="Soundharya Sankar — Frontend Developer. Building responsive, high-performance web applications with clean and maintainable code." />
    <title>Soundharya Sankar — Frontend Developer</title>
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;600;700;800&family=DM+Sans:wght@300;400;500;600;700&display=swap" rel="stylesheet" />
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>

#package.json

{
  "name": "soundharya-portfolio",
  "version": "1.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "@radix-ui/react-toast": "^1.2.7",
    "@radix-ui/react-tooltip": "^1.2.0",
    "@tanstack/react-query": "^5.0.0",
    "class-variance-authority": "^0.7.1",
    "clsx": "^2.1.1",
    "lucide-react": "^0.468.0",
    "react": "^18.3.1",
    "react-dom": "^18.3.1",
    "tailwind-merge": "^2.5.5",
    "wouter": "^3.3.5"
  },
  "devDependencies": {
    "@tailwindcss/vite": "^4.0.0",
    "@types/react": "^18.3.12",
    "@types/react-dom": "^18.3.1",
    "@vitejs/plugin-react": "^4.3.4",
    "tailwindcss": "^4.0.0",
    "typescript": "^5.6.3",
    "vite": "^6.0.5"
  }
}

#tsconfig.json

import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";
import path from "path";

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "src"),
    },
  },
});

import { createRoot } from "react-dom/client";
import App from "./App";
import "./index.css";

createRoot(document.getElementById("root")!).render(<App />);

import { Switch, Route, Router as WouterRouter } from "wouter";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { Toaster } from "@/components/ui/toaster";
import { TooltipProvider } from "@/components/ui/tooltip";
import NotFound from "@/pages/not-found";
import Home from "@/pages/home";

const queryClient = new QueryClient();

function Router() {
  return (
    <Switch>
      <Route path="/" component={Home} />
      <Route component={NotFound} />
    </Switch>
  );
}

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <TooltipProvider>
        <WouterRouter>
          <Router />
        </WouterRouter>
        <Toaster />
      </TooltipProvider>
    </QueryClientProvider>
  );
}

export default App;

@import "tailwindcss";

@theme {
  --color-navy: #0f2540;
  --color-navy-light: #1e4976;
  --color-gold: #c9a84c;
  --color-gold-light: #e8c97b;
  --color-off-white: #f7f5f0;

  --font-sans: "DM Sans", system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  --font-display: "Playfair Display", Georgia, "Times New Roman", serif;
}

@layer base {
  *, *::before, *::after { box-sizing: border-box; }
  html { scroll-behavior: smooth; -webkit-font-smoothing: antialiased; }
  body { margin: 0; font-family: var(--font-sans); background: var(--color-off-white); color: var(--color-navy); }
  ::selection { background: var(--color-gold); color: var(--color-navy); }
}

@layer utilities {
  .bg-navy-section {
    background: var(--color-navy);
    background-image:
      radial-gradient(ellipse 80% 60% at 70% 40%, rgba(201,168,76,0.07) 0%, transparent 70%),
      radial-gradient(ellipse 60% 80% at 10% 80%, rgba(30,73,118,0.4) 0%, transparent 60%);
    color: var(--color-off-white);
  }
  .bg-offwhite-section { background: var(--color-off-white); color: var(--color-navy); }

  .reveal-on-scroll {
    opacity: 0;
    transform: translateY(24px);
    transition: opacity 0.7s ease-out, transform 0.7s ease-out;
  }
  .reveal-up { opacity: 1 !important; transform: translateY(0) !important; }

  .delay-100 { transition-delay: 100ms; animation-delay: 100ms; }
  .delay-200 { transition-delay: 200ms; animation-delay: 200ms; }
  .delay-300 { transition-delay: 300ms; animation-delay: 300ms; }
  .delay-400 { transition-delay: 400ms; animation-delay: 400ms; }

  .reveal-up:not(.reveal-on-scroll) { animation: fadeInUp 0.8s ease-out both; }

  @keyframes fadeInUp {
    from { opacity: 0; transform: translateY(24px); }
    to   { opacity: 1; transform: translateY(0); }
  }
}

.container { width: 100%; margin-left: auto; margin-right: auto; max-width: 1200px; }

import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}

import * as React from "react"
import type { ToastActionElement, ToastProps } from "@/components/ui/toast"

const TOAST_LIMIT = 1
const TOAST_REMOVE_DELAY = 1000000

type ToasterToast = ToastProps & {
  id: string
  title?: React.ReactNode
  description?: React.ReactNode
  action?: ToastActionElement
}

const actionTypes = {
  ADD_TOAST: "ADD_TOAST",
  UPDATE_TOAST: "UPDATE_TOAST",
  DISMISS_TOAST: "DISMISS_TOAST",
  REMOVE_TOAST: "REMOVE_TOAST",
} as const

let count = 0
function genId() {
  count = (count + 1) % Number.MAX_SAFE_INTEGER
  return count.toString()
}

type ActionType = typeof actionTypes
type Action =
  | { type: ActionType["ADD_TOAST"]; toast: ToasterToast }
  | { type: ActionType["UPDATE_TOAST"]; toast: Partial<ToasterToast> }
  | { type: ActionType["DISMISS_TOAST"]; toastId?: ToasterToast["id"] }
  | { type: ActionType["REMOVE_TOAST"]; toastId?: ToasterToast["id"] }

interface State { toasts: ToasterToast[] }

const toastTimeouts = new Map<string, ReturnType<typeof setTimeout>>()

const addToRemoveQueue = (toastId: string) => {
  if (toastTimeouts.has(toastId)) return
  const timeout = setTimeout(() => {
    toastTimeouts.delete(toastId)
    dispatch({ type: "REMOVE_TOAST", toastId })
  }, TOAST_REMOVE_DELAY)
  toastTimeouts.set(toastId, timeout)
}

export const reducer = (state: State, action: Action): State => {
  switch (action.type) {
    case "ADD_TOAST":
      return { ...state, toasts: [action.toast, ...state.toasts].slice(0, TOAST_LIMIT) }
    case "UPDATE_TOAST":
      return { ...state, toasts: state.toasts.map((t) => t.id === action.toast.id ? { ...t, ...action.toast } : t) }
    case "DISMISS_TOAST": {
      const { toastId } = action
      if (toastId) { addToRemoveQueue(toastId) } else { state.toasts.forEach((toast) => addToRemoveQueue(toast.id)) }
      return { ...state, toasts: state.toasts.map((t) => t.id === toastId || toastId === undefined ? { ...t, open: false } : t) }
    }
    case "REMOVE_TOAST":
      if (action.toastId === undefined) return { ...state, toasts: [] }
      return { ...state, toasts: state.toasts.filter((t) => t.id !== action.toastId) }
  }
}

const listeners: Array<(state: State) => void> = []
let memoryState: State = { toasts: [] }

function dispatch(action: Action) {
  memoryState = reducer(memoryState, action)
  listeners.forEach((listener) => listener(memoryState))
}

type Toast = Omit<ToasterToast, "id">

function toast({ ...props }: Toast) {
  const id = genId()
  const update = (props: ToasterToast) => dispatch({ type: "UPDATE_TOAST", toast: { ...props, id } })
  const dismiss = () => dispatch({ type: "DISMISS_TOAST", toastId: id })
  dispatch({ type: "ADD_TOAST", toast: { ...props, id, open: true, onOpenChange: (open) => { if (!open) dismiss() } } })
  return { id, dismiss, update }
}

function useToast() {
  const [state, setState] = React.useState<State>(memoryState)
  React.useEffect(() => {
    listeners.push(setState)
    return () => {
      const index = listeners.indexOf(setState)
      if (index > -1) listeners.splice(index, 1)
    }
  }, [state])
  return { ...state, toast, dismiss: (toastId?: string) => dispatch({ type: "DISMISS_TOAST", toastId }) }
}

export { useToast, toast }

import * as React from "react"
import * as ToastPrimitives from "@radix-ui/react-toast"
import { cva, type VariantProps } from "class-variance-authority"
import { X } from "lucide-react"
import { cn } from "@/lib/utils"

const ToastProvider = ToastPrimitives.Provider

const ToastViewport = React.forwardRef<
  React.ElementRef<typeof ToastPrimitives.Viewport>,
  React.ComponentPropsWithoutRef<typeof ToastPrimitives.Viewport>
>(({ className, ...props }, ref) => (
  <ToastPrimitives.Viewport ref={ref} className={cn("fixed top-0 z-[100] flex max-h-screen w-full flex-col-reverse p-4 sm:bottom-0 sm:right-0 sm:top-auto sm:flex-col md:max-w-[420px]", className)} {...props} />
))
ToastViewport.displayName = ToastPrimitives.Viewport.displayName

const toastVariants = cva(
  "group pointer-events-auto relative flex w-full items-center justify-between space-x-4 overflow-hidden rounded-md border p-6 pr-8 shadow-lg transition-all data-[swipe=cancel]:translate-x-0 data-[swipe=end]:translate-x-[var(--radix-toast-swipe-end-x)] data-[swipe=move]:translate-x-[var(--radix-toast-swipe-move-x)] data-[swipe=move]:transition-none data-[state=open]:animate-in data-[state=closed]:animate-out data-[swipe=end]:animate-out data-[state=closed]:fade-out-80 data-[state=closed]:slide-out-to-right-full data-[state=open]:slide-in-from-top-full data-[state=open]:sm:slide-in-from-bottom-full",
  { variants: { variant: { default: "border bg-background text-foreground", destructive: "destructive group border-destructive bg-destructive text-destructive-foreground" } }, defaultVariants: { variant: "default" } }
)

const Toast = React.forwardRef<
  React.ElementRef<typeof ToastPrimitives.Root>,
  React.ComponentPropsWithoutRef<typeof ToastPrimitives.Root> & VariantProps<typeof toastVariants>
>(({ className, variant, ...props }, ref) => (
  <ToastPrimitives.Root ref={ref} className={cn(toastVariants({ variant }), className)} {...props} />
))
Toast.displayName = ToastPrimitives.Root.displayName

const ToastAction = React.forwardRef<
  React.ElementRef<typeof ToastPrimitives.Action>,
  React.ComponentPropsWithoutRef<typeof ToastPrimitives.Action>
>(({ className, ...props }, ref) => (
  <ToastPrimitives.Action ref={ref} className={cn("inline-flex h-8 shrink-0 items-center justify-center rounded-md border bg-transparent px-3 text-sm font-medium transition-colors hover:bg-secondary focus:outline-none focus:ring-2 disabled:pointer-events-none disabled:opacity-50", className)} {...props} />
))
ToastAction.displayName = ToastPrimitives.Action.displayName

const ToastClose = React.forwardRef<
  React.ElementRef<typeof ToastPrimitives.Close>,
  React.ComponentPropsWithoutRef<typeof ToastPrimitives.Close>
>(({ className, ...props }, ref) => (
  <ToastPrimitives.Close ref={ref} className={cn("absolute right-2 top-2 rounded-md p-1 text-foreground/50 opacity-0 transition-opacity hover:text-foreground focus:opacity-100 focus:outline-none focus:ring-2 group-hover:opacity-100 group-[.destructive]:text-red-300 group-[.destructive]:hover:text-red-50", className)} toast-close="" {...props}>
    <X className="h-4 w-4" />
  </ToastPrimitives.Close>
))
ToastClose.displayName = ToastPrimitives.Close.displayName

const ToastTitle = React.forwardRef<
  React.ElementRef<typeof ToastPrimitives.Title>,
  React.ComponentPropsWithoutRef<typeof ToastPrimitives.Title>
>(({ className, ...props }, ref) => (
  <ToastPrimitives.Title ref={ref} className={cn("text-sm font-semibold", className)} {...props} />
))
ToastTitle.displayName = ToastPrimitives.Title.displayName

const ToastDescription = React.forwardRef<
  React.ElementRef<typeof ToastPrimitives.Description>,
  React.ComponentPropsWithoutRef<typeof ToastPrimitives.Description>
>(({ className, ...props }, ref) => (
  <ToastPrimitives.Description ref={ref} className={cn("text-sm opacity-90", className)} {...props} />
))
ToastDescription.displayName = ToastPrimitives.Description.displayName

type ToastProps = React.ComponentPropsWithoutRef<typeof Toast>
type ToastActionElement = React.ReactElement<typeof ToastAction>

export { type ToastProps, type ToastActionElement, ToastProvider, ToastViewport, Toast, ToastTitle, ToastDescription, ToastClose, ToastAction }

import { useToast } from "@/hooks/use-toast"
import { Toast, ToastClose, ToastDescription, ToastProvider, ToastTitle, ToastViewport } from "@/components/ui/toast"

export function Toaster() {
  const { toasts } = useToast()
  return (
    <ToastProvider>
      {toasts.map(function ({ id, title, description, action, ...props }) {
        return (
          <Toast key={id} {...props}>
            <div className="grid gap-1">
              {title && <ToastTitle>{title}</ToastTitle>}
              {description && <ToastDescription>{description}</ToastDescription>}
            </div>
            {action}
            <ToastClose />
          </Toast>
        )
      })}
      <ToastViewport />
    </ToastProvider>
  )
}

import * as React from "react"
import * as TooltipPrimitive from "@radix-ui/react-tooltip"
import { cn } from "@/lib/utils"

const TooltipProvider = TooltipPrimitive.Provider
const Tooltip = TooltipPrimitive.Root
const TooltipTrigger = TooltipPrimitive.Trigger

const TooltipContent = React.forwardRef<
  React.ElementRef<typeof TooltipPrimitive.Content>,
  React.ComponentPropsWithoutRef<typeof TooltipPrimitive.Content>
>(({ className, sideOffset = 4, ...props }, ref) => (
  <TooltipPrimitive.Portal>
    <TooltipPrimitive.Content ref={ref} sideOffset={sideOffset} className={cn("z-50 overflow-hidden rounded-md bg-primary px-3 py-1.5 text-xs text-primary-foreground animate-in fade-in-0 zoom-in-95", className)} {...props} />
  </TooltipPrimitive.Portal>
))
TooltipContent.displayName = TooltipPrimitive.Content.displayName

export { Tooltip, TooltipTrigger, TooltipContent, TooltipProvider }

import { useEffect } from "react";
import Navbar from "@/components/layout/navbar";
import Hero from "@/components/sections/hero";
import About from "@/components/sections/about";
import Skills from "@/components/sections/skills";
import Projects from "@/components/sections/projects";
import Education from "@/components/sections/education";
import Certifications from "@/components/sections/certifications";
import Contact from "@/components/sections/contact";
import Footer from "@/components/layout/footer";

export default function Home() {
  useEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            entry.target.classList.add("reveal-up");
            observer.unobserve(entry.target);
          }
        });
      },
      { threshold: 0.1, rootMargin: "0px 0px -50px 0px" }
    );
    document.querySelectorAll(".reveal-on-scroll").forEach((el) => observer.observe(el));
    return () => observer.disconnect();
  }, []);

  return (
    <div className="min-h-screen w-full flex flex-col bg-off-white font-sans selection:bg-gold selection:text-navy">
      <Navbar />
      <main className="flex-1 w-full">
        <Hero />
        <About />
        <Skills />
        <Projects />
        <Education />
        <Certifications />
        <Contact />
      </main>
      <Footer />
    </div>
  );
}

import { Card, CardContent } from "@/components/ui/card";

export default function NotFound() {
  return (
    <div className="min-h-screen w-full flex items-center justify-center bg-gray-50">
      <Card className="w-full max-w-md mx-4">
        <CardContent className="pt-6">
          <h1 className="text-2xl font-bold text-gray-900">404 — Page Not Found</h1>
        </CardContent>
      </Card>
    </div>
  );
}

import { useEffect, useState } from "react";
import { Menu, X } from "lucide-react";

function cn(...classes: (string | boolean | undefined)[]) {
  return classes.filter(Boolean).join(" ");
}

export default function Navbar() {
  const [scrolled, setScrolled] = useState(false);
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);

  useEffect(() => {
    const handleScroll = () => setScrolled(window.scrollY > 50);
    window.addEventListener("scroll", handleScroll);
    return () => window.removeEventListener("scroll", handleScroll);
  }, []);

  const scrollTo = (id: string) => {
    setMobileMenuOpen(false);
    document.getElementById(id)?.scrollIntoView({ behavior: "smooth" });
  };

  const navLinks = [
    { name: "About", id: "about" },
    { name: "Skills", id: "skills" },
    { name: "Projects", id: "projects" },
    { name: "Education", id: "education" },
    { name: "Certifications", id: "certifications" },
  ];

  return (
    <header className={cn("fixed top-0 left-0 right-0 z-50 transition-all duration-300 border-b border-transparent", scrolled ? "bg-navy/90 backdrop-blur-md border-white/10 shadow-sm" : "bg-transparent py-4")}>
      <div className="container mx-auto px-4 md:px-6 h-16 flex items-center justify-between">
        <div className="font-display font-bold text-xl tracking-tight cursor-pointer text-off-white" onClick={() => scrollTo("hero")}>
          Soundharya Sankar
        </div>
        <nav className="hidden md:flex items-center gap-8">
          {navLinks.map((link) => (
            <button key={link.id} onClick={() => scrollTo(link.id)} className="text-sm font-medium text-off-white/80 hover:text-gold transition-colors">
              {link.name}
            </button>
          ))}
          <button onClick={() => scrollTo("contact")} className="text-sm font-medium text-navy bg-gold hover:bg-gold-light px-6 py-2.5 rounded-sm transition-colors">
            Contact
          </button>
        </nav>
        <button className="md:hidden text-off-white p-2" onClick={() => setMobileMenuOpen(!mobileMenuOpen)}>
          {mobileMenuOpen ? <X size={24} /> : <Menu size={24} />}
        </button>
      </div>
      {mobileMenuOpen && (
        <div className="md:hidden absolute top-full left-0 right-0 bg-navy border-b border-white/10 shadow-lg py-4 px-4 flex flex-col gap-4">
          {navLinks.map((link) => (
            <button key={link.id} onClick={() => scrollTo(link.id)} className="text-left text-sm font-medium text-off-white/80 hover:text-gold transition-colors py-2">
              {link.name}
            </button>
          ))}
          <button onClick={() => scrollTo("contact")} className="text-sm font-medium text-navy bg-gold hover:bg-gold-light px-6 py-2.5 rounded-sm transition-colors mt-2">
            Contact
          </button>
        </div>
      )}
    </header>
  );
}

export default function Footer() {
  return (
    <footer className="py-8 bg-[#0a1829] text-off-white/60">
      <div className="container px-4 md:px-6 mx-auto flex flex-col items-center justify-center gap-4">
        <p className="text-sm text-center">
          Designed & built by <span className="text-gold font-medium">Soundharya Sankar</span> · © 2026
        </p>
      </div>
    </footer>
  );
}

import { ArrowRight, Linkedin, Github } from "lucide-react";

export default function Hero() {
  const scrollToContact = () => {
    document.getElementById("contact")?.scrollIntoView({ behavior: "smooth" });
  };

  const stats = [
    { value: "5+", label: "Web interfaces deployed" },
    { value: "8.7", label: "CGPA (B.Sc. CS)" },
    { value: "3+", label: "Certifications earned" },
    { value: "2",  label: "Projects built" },
  ];

  return (
    <section id="hero" className="bg-navy-section relative min-h-[90vh] flex flex-col justify-center pt-24 pb-16 overflow-hidden">
      <div className="container px-4 md:px-6 mx-auto relative z-10">
        <div className="grid lg:grid-cols-2 gap-12 lg:gap-8 items-center">
          <div className="space-y-8">
            <div className="inline-flex items-center gap-2 reveal-up">
              <span className="w-8 h-[1px] bg-gold" />
              <span className="text-xs font-bold tracking-widest text-gold uppercase">Frontend Developer</span>
            </div>
            <h1 className="text-5xl md:text-6xl lg:text-7xl font-bold tracking-tight reveal-up delay-100 text-off-white">
              Soundharya <br /><span className="text-gold">Sankar.</span>
            </h1>
            <p className="text-lg md:text-xl text-off-white/80 max-w-xl font-light leading-relaxed reveal-up delay-200">
              Building responsive, high-performance web applications with clean and maintainable code. Passionate about delivering exceptional user experiences.
            </p>
            <div className="flex flex-col sm:flex-row gap-4 pt-4 reveal-up delay-300">
              <button onClick={scrollToContact} className="inline-flex items-center justify-center h-14 px-8 text-base font-medium text-navy bg-gold hover:bg-gold-light transition-colors rounded-sm group">
                Let's Work Together
                <ArrowRight className="ml-2 h-4 w-4 group-hover:translate-x-1 transition-transform" />
              </button>
              <div className="flex items-center gap-4 pl-0 sm:pl-4">
                <a href="https://github.com/soundharya" target="_blank" rel="noreferrer" className="w-12 h-12 rounded-full border border-white/20 flex items-center justify-center text-off-white hover:border-gold hover:text-gold transition-colors">
                  <Github size={20} />
                </a>
                <a href="https://www.linkedin.com/in/soundharya-sankar-aa903539b" target="_blank" rel="noreferrer" className="w-12 h-12 rounded-full border border-white/20 flex items-center justify-center text-off-white hover:border-gold hover:text-gold transition-colors">
                  <Linkedin size={20} />
                </a>
              </div>
            </div>
          </div>
          <div className="grid grid-cols-2 gap-4 lg:pl-12 reveal-up delay-400">
            {stats.map((stat, i) => (
              <div key={i} className="p-6 border border-white/10 bg-white/5 rounded-sm hover:border-gold/50 transition-colors">
                <div className="text-3xl md:text-4xl font-display font-bold text-gold mb-2">{stat.value}</div>
                <div className="text-sm text-off-white/70">{stat.label}</div>
              </div>
            ))}
          </div>
        </div>
      </div>
    </section>
  );
}

export default function About() {
  return (
    <section id="about" className="bg-offwhite-section py-24 md:py-32 relative">
      <div className="container px-4 md:px-6 mx-auto">
        <div className="grid md:grid-cols-[1fr_2fr] gap-12 lg:gap-24 items-start">
          <div className="reveal-on-scroll">
            <div className="inline-flex items-center gap-2 mb-6">
              <span className="w-8 h-[1px] bg-gold" />
              <span className="text-xs font-bold tracking-widest text-navy uppercase">— About Me</span>
            </div>
            <h2 className="text-4xl md:text-5xl font-display font-bold text-navy">Crafting Digital Experiences.</h2>
            <div className="mt-8 w-32 h-32 bg-navy rounded-sm flex items-center justify-center border-4 border-gold/30">
              <span className="text-4xl font-display text-gold">SS</span>
            </div>
          </div>
          <div className="space-y-6 text-lg text-navy/80 leading-relaxed reveal-on-scroll delay-200">
            <p>I'm a Frontend Developer with hands-on experience building responsive, cross-browser web applications using HTML5, CSS3, and JavaScript. I'm deeply passionate about delivering high-performance digital solutions backed by clean, maintainable code.</p>
            <p>Currently pursuing my B.Sc. in Computer Science (CGPA: 8.7) at Krishnasamy College of Science, Arts and Management for Women, Cuddalore — affiliated to Annamalai University — I bring a strong foundation in Python and SQL alongside my frontend expertise.</p>
            <p>I believe great web development sits at the intersection of thoughtful design and solid engineering — and I strive to bring both to every project I work on.</p>
          </div>
        </div>
      </div>
    </section>
  );
}

import { Code2, Cpu, Wrench, Telescope } from "lucide-react";

export default function Skills() {
  const skills = [
    { title: "Frontend",         icon: <Code2 size={24} />,    items: "HTML5, CSS3, JavaScript ES6+, Responsive Design, DOM Manipulation, Cross-Browser Compatibility" },
    { title: "Programming",      icon: <Cpu size={24} />,      items: "Python, SQL" },
    { title: "Tools & Platforms",icon: <Wrench size={24} />,   items: "Git, GitHub, Microsoft Excel" },
    { title: "Exposure",         icon: <Telescope size={24} />,items: "REST APIs, Blockchain, Machine Learning, UI/UX Design" },
  ];

  return (
    <section id="skills" className="bg-navy-section py-24 md:py-32 relative">
      <div className="container px-4 md:px-6 mx-auto">
        <div className="reveal-on-scroll text-center mb-16">
          <div className="inline-flex items-center gap-2 mb-4 justify-center">
            <span className="w-8 h-[1px] bg-gold" />
            <span className="text-xs font-bold tracking-widest text-gold uppercase">— Technical Arsenal</span>
            <span className="w-8 h-[1px] bg-gold" />
          </div>
          <h2 className="text-4xl md:text-5xl font-display font-bold text-off-white">Skills & Expertise</h2>
        </div>
        <div className="grid md:grid-cols-2 lg:grid-cols-4 gap-6">
          {skills.map((skill, index) => (
            <div key={index} className="bg-white/5 border border-white/10 p-8 rounded-sm hover:border-gold/50 transition-colors reveal-on-scroll">
              <div className="w-12 h-12 bg-gold/10 text-gold rounded-sm flex items-center justify-center mb-6">{skill.icon}</div>
              <h3 className="text-xl font-display font-bold text-off-white mb-4">{skill.title}</h3>
              <p className="text-off-white/70 text-sm leading-relaxed">{skill.items}</p>
            </div>
          ))}
        </div>
      </div>
    </section>
  );
}

import { ArrowUpRight } from "lucide-react";
import financeImg from "@/assets/images/project-finance.png";
import webImg from "@/assets/images/project-web.png";

export default function Projects() {
  const projects = [
    {
      title: "Micro-Investment Platform",
      tag: "Blockchain · Fintech",
      subtitle: "A decentralized microfinance system for small-scale investors",
      bullets: [
        "Engineered a decentralized microfinance system facilitating collateral-free lending and transparent fund transfers.",
        "Implemented a trust-based repayment architecture using community-validated data models.",
        "Developed a secure transaction ledger using blockchain principles for end-to-end auditability.",
        "Optimized SQL queries for real-time tracking of savings and investment activities.",
      ],
      tech: "Python, SQL, Blockchain",
      link: "https://github.com/soundharya",
      image: financeImg,
    },
    {
      title: "Responsive Portfolio Website",
      tag: "Frontend · Web",
      subtitle: "A fully responsive, mobile-first personal portfolio",
      bullets: [
        "Built a fully responsive personal portfolio website optimized for mobile-first design.",
        "Implemented smooth scroll navigation, CSS animations, and dynamic content rendering using vanilla JavaScript.",
        "Achieved consistent layout across Chrome, Firefox, and Safari using CSS Flexbox and Grid.",
      ],
      tech: "HTML5, CSS3, JavaScript",
      link: "https://github.com/soundharya",
      image: webImg,
    },
  ];

  return (
    <section id="projects" className="bg-offwhite-section py-24 md:py-32 relative">
      <div className="container px-4 md:px-6 mx-auto">
        <div className="reveal-on-scroll mb-16">
          <div className="inline-flex items-center gap-2 mb-4">
            <span className="w-8 h-[1px] bg-gold" />
            <span className="text-xs font-bold tracking-widest text-navy uppercase">— Selected Work</span>
          </div>
          <h2 className="text-4xl md:text-5xl font-display font-bold text-navy">Featured Projects</h2>
        </div>
        <div className="grid lg:grid-cols-2 gap-12">
          {projects.map((project, index) => (
            <div key={index} className="group bg-white border border-navy/10 rounded-sm overflow-hidden hover:shadow-xl transition-all duration-500 reveal-on-scroll">
              <a href={project.link} target="_blank" rel="noreferrer" className="block relative aspect-video overflow-hidden">
                <div className="absolute inset-0 bg-gold/20 opacity-0 group-hover:opacity-100 transition-opacity duration-500 z-10" />
                <img src={project.image} alt={project.title} className="w-full h-full object-cover transition-transform duration-700 group-hover:scale-105" />
              </a>
              <div className="p-8">
                <div className="flex justify-between items-start mb-4">
                  <div>
                    <span className="text-xs font-bold tracking-wider text-gold uppercase mb-2 block">{project.tag}</span>
                    <h3 className="text-2xl font-display font-bold text-navy group-hover:text-gold transition-colors">{project.title}</h3>
                  </div>
                  <a href={project.link} target="_blank" rel="noreferrer" className="p-2 text-navy hover:text-gold transition-colors">
                    <ArrowUpRight size={24} />
                  </a>
                </div>
                <p className="text-navy/80 font-medium mb-6">{project.subtitle}</p>
                <ul className="space-y-3 mb-8">
                  {project.bullets.map((bullet, i) => (
                    <li key={i} className="text-navy/70 text-sm flex items-start gap-2">
                      <span className="text-gold mt-1">•</span>
                      <span>{bullet}</span>
                    </li>
                  ))}
                </ul>
                <div className="pt-6 border-t border-navy/10">
                  <span className="text-xs font-bold text-navy bg-navy/5 px-3 py-1 rounded-sm uppercase tracking-wider">{project.tech}</span>
                </div>
              </div>
            </div>
          ))}
        </div>
      </div>
    </section>
  );
}

import { GraduationCap, Award, Calendar } from "lucide-react";

export default function Education() {
  return (
    <section id="education" className="bg-offwhite-section py-24 md:py-32 relative border-t border-navy/5">
      <div className="container px-4 md:px-6 mx-auto">
        <div className="max-w-3xl mx-auto">
          <div className="reveal-on-scroll mb-14 text-center">
            <div className="inline-flex items-center gap-2 mb-4 justify-center">
              <span className="w-8 h-[1px] bg-gold" />
              <span className="text-xs font-bold tracking-widest text-navy uppercase">Academic Background</span>
              <span className="w-8 h-[1px] bg-gold" />
            </div>
            <h2 className="text-4xl md:text-5xl font-display font-bold text-navy">Education</h2>
          </div>
          <div className="reveal-on-scroll delay-100">
            <div className="relative bg-white border border-navy/10 rounded-sm overflow-hidden hover:border-gold/50 hover:shadow-xl transition-all duration-300 group">
              <div className="absolute left-0 top-0 bottom-0 w-1 bg-gold"></div>
              <div className="p-8 md:p-10">
                <div className="flex flex-col md:flex-row md:items-start md:justify-between gap-6 mb-6">
                  <div className="flex items-start gap-4">
                    <div className="w-12 h-12 rounded-sm bg-navy/5 border border-navy/10 flex items-center justify-center flex-shrink-0 group-hover:bg-gold/10 group-hover:border-gold/30 transition-colors">
                      <GraduationCap className="w-6 h-6 text-navy group-hover:text-gold transition-colors" strokeWidth={1.5} />
                    </div>
                    <div>
                      <h3 className="text-2xl md:text-3xl font-display font-bold text-navy mb-1">B.Sc. Computer Science</h3>
                      <p className="text-navy/70 font-medium">Krishnasamy College of Science, Arts and Management for Women</p>
                      <p className="text-navy/50 text-sm mt-1">Cuddalore · Affiliated to Annamalai University</p>
                    </div>
                  </div>
                  <div className="inline-flex items-center gap-2 bg-navy text-off-white px-4 py-2 rounded-sm text-xs font-bold tracking-widest uppercase self-start whitespace-nowrap">
                    <Calendar className="w-3.5 h-3.5" strokeWidth={2} />
                    Jul 2023 – May 2026
                  </div>
                </div>
                <div className="grid grid-cols-1 sm:grid-cols-3 gap-3 pt-6 border-t border-navy/10">
                  <div className="flex items-center gap-3 px-4 py-3 bg-navy/[0.03] rounded-sm">
                    <Award className="w-5 h-5 text-gold flex-shrink-0" strokeWidth={1.5} />
                    <div>
                      <div className="text-[10px] font-bold tracking-widest uppercase text-navy/50">CGPA</div>
                      <div className="text-base font-bold text-navy">8.7 / 10</div>
                    </div>
                  </div>
                  <div className="flex items-center gap-3 px-4 py-3 bg-navy/[0.03] rounded-sm">
                    <div className="w-5 h-5 rounded-full border-2 border-gold flex items-center justify-center flex-shrink-0">
                      <div className="w-1.5 h-1.5 rounded-full bg-gold"></div>
                    </div>
                    <div>
                      <div className="text-[10px] font-bold tracking-widest uppercase text-navy/50">Progress</div>
                      <div className="text-base font-bold text-navy">6th Semester</div>
                    </div>
                  </div>
                  <div className="flex items-center gap-3 px-4 py-3 bg-navy/[0.03] rounded-sm">
                    <div className="w-5 h-5 rounded-full bg-gold/20 border border-gold/40 flex-shrink-0"></div>
                    <div>
                      <div className="text-[10px] font-bold tracking-widest uppercase text-navy/50">Status</div>
                      <div className="text-base font-bold text-navy">In Progress</div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  );
}

export default function Certifications() {
  const certs = [
    { title: "Web Development Internship", org: "NextGen Solutions", year: "2025",     desc: "Hands-on frontend development experience building production-ready web interfaces." },
    { title: "UI/UX Design Master Class",  org: "NoviTech R&D",     year: "Completed", desc: "Comprehensive training in user interface and user experience design principles." },
    { title: "Machine Learning & AI Webinar", org: "GUVI × HCL",   year: "Attended",  desc: "Attended industry webinar covering machine learning fundamentals and AI applications." },
  ];

  return (
    <section id="certifications" className="bg-offwhite-section py-24 md:py-32 relative border-t border-navy/5">
      <div className="container px-4 md:px-6 mx-auto">
        <div className="reveal-on-scroll mb-16">
          <div className="inline-flex items-center gap-2 mb-4">
            <span className="w-8 h-[1px] bg-gold" />
            <span className="text-xs font-bold tracking-widest text-navy uppercase">— Continuous Learning</span>
          </div>
          <h2 className="text-4xl md:text-5xl font-display font-bold text-navy">Certifications & Training</h2>
        </div>
        <div className="grid md:grid-cols-3 gap-6">
          {certs.map((cert, index) => (
            <div key={index} className="bg-white border border-navy/10 p-8 rounded-sm hover:border-gold/50 transition-colors hover:-translate-y-1 duration-300 reveal-on-scroll">
              <div className="text-xs font-bold text-gold uppercase tracking-wider mb-4 flex justify-between items-center">
                <span>{cert.org}</span>
                <span className="bg-gold/10 px-2 py-1 rounded-sm">{cert.year}</span>
              </div>
              <h3 className="text-xl font-display font-bold text-navy mb-4">{cert.title}</h3>
              <p className="text-navy/70 text-sm leading-relaxed">{cert.desc}</p>
            </div>
          ))}
        </div>
      </div>
    </section>
  );
}

import { useState } from "react";
import { Mail, Phone, MapPin, Linkedin, Send, Loader2 } from "lucide-react";
import { useToast } from "@/hooks/use-toast";

export default function Contact() {
  const { toast } = useToast();
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [message, setMessage] = useState("");

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    const form = e.currentTarget;
    setIsSubmitting(true);
    try {
      const formData = new FormData(form);
      formData.append("_subject", `Portfolio contact from ${formData.get("name")}`);
      formData.append("_template", "table");
      formData.append("_captcha", "false");
      const response = await fetch("https://formsubmit.co/ajax/soundharya6263@gmail.com", {
        method: "POST",
        headers: { Accept: "application/json" },
        body: formData,
      });
      if (!response.ok) throw new Error("Submission failed");
      form.reset();
      setMessage("");
      toast({ title: "Message sent", description: "Thanks for reaching out! I'll get back to you soon." });
    } catch {
      toast({ title: "Couldn't send message", description: "Something went wrong. Please email me directly at soundharya6263@gmail.com.", variant: "destructive" });
    } finally {
      setIsSubmitting(false);
    }
  };

  return (
    <section id="contact" className="bg-navy-section py-24 md:py-32 relative">
      <div className="container px-4 md:px-6 mx-auto relative z-10">
        <div className="grid lg:grid-cols-2 gap-16 items-start">
          <div className="reveal-on-scroll">
            <div className="inline-flex items-center gap-2 mb-6">
              <span className="w-8 h-[1px] bg-gold" />
              <span className="text-xs font-bold tracking-widest text-gold uppercase">— Contact</span>
            </div>
            <h2 className="text-4xl md:text-5xl font-display font-bold text-off-white mb-6">Let's Work Together</h2>
            <p className="text-lg text-off-white/70 mb-12 max-w-md leading-relaxed">I'm currently open to frontend development opportunities, internships, and collaborations. Feel free to reach out!</p>
            <div className="space-y-6">
              <a href="mailto:soundharya6263@gmail.com" className="flex items-center gap-4 text-off-white/80 hover:text-gold transition-colors group">
                <div className="w-12 h-12 bg-white/5 border border-white/10 flex items-center justify-center rounded-sm group-hover:border-gold/50 transition-colors"><Mail size={20} className="text-gold" /></div>
                <div><div className="text-sm text-off-white/50 mb-1 uppercase tracking-wider font-bold">Email</div><div className="font-medium">soundharya6263@gmail.com</div></div>
              </a>
              <a href="tel:+918300742415" className="flex items-center gap-4 text-off-white/80 hover:text-gold transition-colors group">
                <div className="w-12 h-12 bg-white/5 border border-white/10 flex items-center justify-center rounded-sm group-hover:border-gold/50 transition-colors"><Phone size={20} className="text-gold" /></div>
                <div><div className="text-sm text-off-white/50 mb-1 uppercase tracking-wider font-bold">Phone</div><div className="font-medium">(+91) 8300742415</div></div>
              </a>
              <a href="https://www.linkedin.com/in/soundharya-sankar-aa903539b" target="_blank" rel="noreferrer" className="flex items-center gap-4 text-off-white/80 hover:text-gold transition-colors group">
                <div className="w-12 h-12 bg-white/5 border border-white/10 flex items-center justify-center rounded-sm group-hover:border-gold/50 transition-colors"><Linkedin size={20} className="text-gold" /></div>
                <div><div className="text-sm text-off-white/50 mb-1 uppercase tracking-wider font-bold">LinkedIn</div><div className="font-medium">linkedin.com/in/soundharya-sankar</div></div>
              </a>
              <div className="flex items-center gap-4 text-off-white/80">
                <div className="w-12 h-12 bg-white/5 border border-white/10 flex items-center justify-center rounded-sm"><MapPin size={20} className="text-gold" /></div>
                <div><div className="text-sm text-off-white/50 mb-1 uppercase tracking-wider font-bold">Location</div><div className="font-medium">Cuddalore, Tamil Nadu, India</div></div>
              </div>
            </div>
          </div>
          <div className="bg-white/5 border border-white/10 p-8 rounded-sm reveal-on-scroll delay-200">
            <form onSubmit={handleSubmit} className="space-y-6">
              <div className="grid md:grid-cols-2 gap-6">
                <div className="space-y-2">
                  <label htmlFor="name" className="text-sm font-bold text-off-white/70 uppercase tracking-wider">Name</label>
                  <input id="name" name="name" required placeholder="Your Name" className="w-full bg-navy/50 border border-white/10 h-12 px-4 rounded-sm text-off-white focus:outline-none focus:border-gold transition-colors" />
                </div>
                <div className="space-y-2">
                  <label htmlFor="email" className="text-sm font-bold text-off-white/70 uppercase tracking-wider">Email</label>
                  <input id="email" name="email" type="email" required placeholder="your@email.com" className="w-full bg-navy/50 border border-white/10 h-12 px-4 rounded-sm text-off-white focus:outline-none focus:border-gold transition-colors" />
                </div>
              </div>
              <div className="space-y-2">
                <label htmlFor="subject" className="text-sm font-bold text-off-white/70 uppercase tracking-wider">Subject</label>
                <input id="subject" name="subject" required placeholder="What is this regarding?" className="w-full bg-navy/50 border border-white/10 h-12 px-4 rounded-sm text-off-white focus:outline-none focus:border-gold transition-colors" />
              </div>
              <div className="space-y-2">
                <div className="flex justify-between">
                  <label htmlFor="message" className="text-sm font-bold text-off-white/70 uppercase tracking-wider">Message</label>
                  <span className="text-xs text-off-white/40">{message.length}/500</span>
                </div>
                <textarea id="message" name="message" required maxLength={500} value={message} onChange={(e) => setMessage(e.target.value)} placeholder="Tell me"
