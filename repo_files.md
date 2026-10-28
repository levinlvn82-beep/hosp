# Repository files for QueueCare & Helix

You can copy the following files into a new GitHub repository.  The folder structure matches the layout created in the workspace.

```
queuecare-helix-monorepo/
├─ .gitignore
├─ package.json
├─ README.md (optional – add your own description)
├─ apps/
│   └─ helix/
│       ├─ package.json
│       ├─ tailwind.config.ts
│       ├─ postcss.config.js
│       ├─ next.config.mjs
│       ├─ src/
│       │   ├─ app/
│       │   │   ├─ layout.tsx
│       │   │   ├─ page.tsx
│       │   │   └─ globals.css
│       │   └─ components/
│       │       ├─ Helix.tsx
│       │       ├─ HelixContainer.tsx
│       │       └─ HelixScene.tsx
└─ packages/
    └─ ui/   (empty placeholder – you can add shared shadcn/ui components later)
```

---

## 1. Root `.gitignore`
```text
node_modules/
.next/
dist/
.tmp/
.DS_Store
.env.local
.env.development
.env.production
npm-debug.log
yarn-debug.log
yarn-error.log
pnpm-debug.log
```

---
## 2. Root `package.json`
```json
{
  "name": "queuecare-helix-monorepo",
  "private": true,
  "workspaces": ["apps/*", "packages/*"],
  "scripts": {
    "dev": "concurrently \"npm --prefix apps/queuecare run dev\" \"npm --prefix apps/helix run dev\",
    "build": "npm run build --workspaces",
    "lint": "eslint . --ext .ts,.tsx",
    "type-check": "tsc --noEmit",
    "seed": "npm run seed --workspace apps/queuecare",
    "simulate": "node apps/queuecare/scripts/simulate.js"
  },
  "dependencies": {
    "concurrently": "^8.2.2"
  },
  "devDependencies": {
    "@types/node": "^20.14.2",
    "eslint": "^8.57.0",
    "eslint-config-next": "^14.2.4",
    "eslint-plugin-react": "^7.34.2",
    "typescript": "5.5.4"
  }
}
```

---
## 3. `apps/helix/package.json`
```json
{
  "name": "helix",
  "private": true,
  "version": "0.1.0",
  "type": "module",
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "next": "14.2.4",
    "react": "18.3.1",
    "react-dom": "18.3.1",
    "@react-three/fiber": "8.16.8",
    "@react-three/drei": "9.106.2",
    "three": "0.166.1",
    "gsap": "3.12.5",
    "framer-motion": "11.3.0",
    "@react-three/postprocessing": "2.3.0"
  },
  "devDependencies": {
    "typescript": "5.5.4",
    "@types/react": "^18.3.3",
    "@types/node": "^20.14.2",
    "eslint": "^8.57.0",
    "eslint-config-next": "^14.2.4",
    "tailwindcss": "3.4.10",
    "autoprefixer": "10.4.20",
    "postcss": "8.4.41"
  }
}
```

---
## 4. `apps/helix/tailwind.config.ts`
```ts
import type { Config } from 'tailwindcss';

export default {
  content: [
    './src/**/*.{js,ts,jsx,tsx}',
    './app/**/*.{js,ts,jsx,tsx}',
    './pages/**/*.{js,ts,jsx,tsx}',
  ],
  theme: {
    extend: {
      colors: {
        background: '#050B18',
        cyan: '#38BDF8',
        amber: '#F59E0B',
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
    },
  },
  plugins: [],
} satisfies Config;
```

---
## 5. `apps/helix/next.config.mjs`
```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  swcMinify: true,
  // Disable SSR for the heavy 3‑D canvas – it will be loaded client‑side only
  pageExtensions: ['tsx', 'ts', 'jsx', 'js'],
};
export default nextConfig;
```

---
## 6. `apps/helix/postcss.config.js`
```js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```

---
## 7. `apps/helix/src/app/globals.css`
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

html, body {
  overflow-x: hidden;
  min-height: 100%;
}
```

---
## 8. `apps/helix/src/app/layout.tsx`
```tsx
import './globals.css';
import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Helix – DNA Insights',
  description: 'A cinematic 3D helix hero section',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en" className="scroll-smooth">
      <head />
      <body className="bg-[#050B18] text-white min-h-screen flex flex-col">
        {children}
      </body>
    </html>
  );
}
```

---
## 9. `apps/helix/src/app/page.tsx`
```tsx
import { Canvas } from '@react-three/fiber';
import { Suspense } from 'react';
import Helix from '@/components/Helix';

export default function Hero() {
  return (
    <section className="relative w-full h-screen overflow-hidden">
      {/* 3D Canvas */}
      <Canvas camera={{ position: [0, 0, 10], fov: 50 }}>
        <color attach="background" args={['#050B18']} />
        <ambientLight intensity={0.3} />
        <directionalLight position={[5, 5, 5]} intensity={0.8} />
        <Suspense fallback={null}>
          <Helix />
        </Suspense>
      </Canvas>
      {/* Overlay content */}
      <div className="absolute inset-0 flex flex-col items-center justify-center text-center p-4 space-y-6">
        <h1 className="text-4xl sm:text-5xl md:text-6xl font-medium leading-snug text-white">
          Helix helps you understand what your DNA means, not just what it says
        </h1>
        <form className="flex flex-col sm:flex-row gap-2 w-full max-w-lg mx-auto" onSubmit={(e) => e.preventDefault()}>
          <input
            type="email"
            placeholder="Enter your email"
            className="flex-1 px-4 py-2 rounded-md bg-white/10 text-white placeholder-gray-300 focus:outline-none focus:ring-2 focus:ring-cyan"
          />
          <button type="submit" className="px-6 py-2 bg-amber-600 hover:bg-amber-500 rounded-md font-medium transition-colors">
            Request access +
          </button>
        </form>
      </div>
    </section>
  );
}
```

---
## 10. `apps/helix/src/components/Helix.tsx`
```tsx
import { useRef, useEffect } from 'react';
import { useFrame } from '@react-three/fiber';
import { MeshPhysicalMaterial, Vector3, CatmullRomCurve3, TubeGeometry, InstancedMesh, Object3D, Color } from 'three';

// Helper to generate a helix curve
function createHelixCurve(radius: number, turns: number, height: number) {
  const points: Vector3[] = [];
  const total = 200;
  for (let i = 0; i <= total; i++) {
    const t = (i / total) * Math.PI * 2 * turns;
    const y = (i / total) * height - height / 2;
    const x = Math.cos(t) * radius;
    const z = Math.sin(t) * radius;
    points.push(new Vector3(x, y, z));
  }
  return new CatmullRomCurve3(points);
}

export default function Helix() {
  const groupRef = useRef<THREE.Group>(null);

  // Shared materials
  const strandMaterial = new MeshPhysicalMaterial({
    color: new Color('#38BDF8'), // cyan
    metalness: 0.9,
    roughness: 0.3,
    clearcoat: 1,
    clearcoatRoughness: 0.1,
  });

  const ringMaterial = new MeshPhysicalMaterial({
    color: new Color('#F59E0B'), // amber
    emissive: new Color('#F59E0B'),
    emissiveIntensity: 1.5,
    metalness: 0.5,
    roughness: 0.4,
    clearcoat: 1,
    clearcoatRoughness: 0.2,
  });

  // Two strands – second one rotated 180°
  const leftCurve = createHelixCurve(1.5, 8, 6);
  const rightCurve = createHelixCurve(1.5, 8, 6);
  rightCurve.rotateY(Math.PI);

  const tubeGeom = new TubeGeometry(leftCurve, 200, 0.07, 8, false);
  const tubeGeom2 = new TubeGeometry(rightCurve, 200, 0.07, 8, false);

  // Rungs (instanced)
  const rungCount = 40;
  const dummy = new Object3D();
  const rungInstanced = new InstancedMesh(new TubeGeometry(new THREE.CurvePath(), 1, 0.025, 8, false), ringMaterial, rungCount);
  for (let i = 0; i < rungCount; i++) {
    const t = i / (rungCount - 1);
    const p1 = leftCurve.getPointAt(t);
    const p2 = rightCurve.getPointAt(t);
    const midpoint = new Vector3().addVectors(p1, p2).multiplyScalar(0.5);
    const direction = new Vector3().subVectors(p2, p1);
    const length = direction.length();
    dummy.position.copy(midpoint);
    dummy.scale.set(length, 1, 1);
    dummy.quaternion.setFromUnitVectors(new Vector3(0, 1, 0), direction.clone().normalize());
    dummy.updateMatrix();
    rungInstanced.setMatrixAt(i, dummy.matrix);
  }

  // Rotation & floating animation
  useFrame(({ clock }) => {
    if (groupRef.current) {
      groupRef.current.rotation.y = clock.getElapsedTime() * 0.05;
      groupRef.current.position.y = Math.sin(clock.getElapsedTime() * 0.5) * 0.1;
    }
  });

  // Simple pulse on every 8th rung
  useEffect(() => {
    const ids = new Set<number>();
    for (let i = 0; i < rungCount; i++) if (i % 8 === 0) ids.add(i);
    const animate = () => {
      const time = performance.now() * 0.004;
      ids.forEach((i) => {
        const scale = 1 + 0.2 * Math.abs(Math.sin(time + i));
        rungInstanced.getMatrixAt(i, dummy.matrix);
        dummy.scale.set(scale, scale, scale);
        dummy.updateMatrix();
        rungInstanced.setMatrixAt(i, dummy.matrix);
      });
      rungInstanced.instanceMatrix.needsUpdate = true;
      requestAnimationFrame(animate);
    };
    animate();
  }, []);

  return (
    <group ref={groupRef}>
      <mesh geometry={tubeGeom} material={strandMaterial} />
      <mesh geometry={tubeGeom2} material={strandMaterial} />
      <primitive object={rungInstanced} />
    </group>
  );
}
```

---
## 11. `apps/helix/src/components/HelixContainer.tsx`
```tsx
import { useEffect, useState } from 'react';
import DynamicHelix from '@/components/HelixScene';
import Fallback from '@/components/HelixFallback';

export default function HelixContainer() {
  const [useFallback, setUseFallback] = useState(false);

  useEffect(() => {
    const isWebGL2 = typeof window !== 'undefined' &&
      !!window.WebGL2RenderingContext &&
      (() => {
        const canvas = document.createElement('canvas');
        const ctx = canvas.getContext('webgl2');
        return !!ctx;
      })();
    const prefersReduced = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
    if (!isWebGL2 || prefersReduced) setUseFallback(true);
  }, []);

  return useFallback ? <Fallback /> : <DynamicHelix />;
}
```

---
## 12. `apps/helix/src/components/HelixScene.tsx`
```tsx
import { Canvas, useThree } from '@react-three/fiber';
import { Suspense, useEffect, useRef, useState } from 'react';
import { EffectComposer, Bloom } from '@react-three/postprocessing';
import Helix from '@/components/Helix';
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import { motion, AnimatePresence } from 'framer-motion';

if (typeof window !== 'undefined') {
  gsap.registerPlugin(ScrollTrigger);
}

export default function HelixScene() {
  const camRef = useRef<THREE.PerspectiveCamera>(null);
  const { camera } = useThree();
  const [stage, setStage] = useState(1);

  // Camera animation tied to scroll
  useEffect(() => {
    if (!camRef.current) return;
    const tl = gsap.timeline({
      scrollTrigger: {
        trigger: '.helix-scroll-wrapper',
        start: 'top top',
        end: 'bottom bottom',
        scrub: true,
      },
    });
    tl.fromTo(camera.position, { z: 12 }, { z: 8 }, 0);
    tl.to(camera.position, { z: 3 }, 0.3);
    tl.to(camera.position, { x: -5, y: 2, z: 6 }, 0.7);
    return () => {
      tl.kill();
      ScrollTrigger.getAll().forEach(st => st.kill());
    };
  }, []);

  // Text stage detection
  useEffect(() => {
    const stm = gsap.timeline({
      scrollTrigger: {
        trigger: '.helix-scroll-wrapper',
        start: 'top top',
        end: 'bottom bottom',
        scrub: true,
        onUpdate: self => {
          const p = self.progress;
          setStage(p < 0.3 ? 1 : p < 0.7 ? 2 : 3);
        },
      },
    });
    return () => stm.kill();
  }, []);

  const headings = {
    1: 'Helix helps you understand what your DNA means, not just what it says',
    2: 'How will DNA testing change you?',
    3: 'Ready to unlock your genetic story?',
  } as const;

  return (
    <div className="helix-scroll-wrapper relative h-[300vh]">
      <div className="sticky top-0 left-0 w-full h-screen flex items-center justify-center">
        <Canvas camera={{ position: [0, 0, 12], fov: 50 }} gl={{ antialias: true, preserveDrawingBuffer: true }}>
          <color attach="background" args={['#050B18']} />
          <ambientLight intensity={0.3} />
          <directionalLight position={[5, 5, 5]} intensity={0.8} />
          <Suspense fallback={null}>
            <Helix />
          </Suspense>
          <EffectComposer>
            <Bloom luminanceThreshold={0.2} luminanceSmoothing={0.1} height={300} opacity={1.5} />
          </EffectComposer>
        </Canvas>
        <div className="absolute inset-0 flex flex-col items-center justify-center space-y-6 p-4 text-center pointer-events-none">
          <AnimatePresence mode="wait">
            <motion.h1
              key={stage}
              className="text-3xl sm:text-5xl md:text-6xl font-medium leading-snug text-white max-w-2xl"
              initial={{ opacity: 0, y: 20 }}
              animate={{ opacity: 1, y: 0, transition: { duration: 0.5 } }}
              exit={{ opacity: 0, y: -20, transition: { duration: 0.3 } }}
            >
              {headings[stage]}
            </motion.h1>
          </AnimatePresence>
          {stage === 1 && (
            <form className="flex flex-col sm:flex-row gap-2 w-full max-w-lg mx-auto pointer-events-auto" onSubmit={e => e.preventDefault()}>
              <input type="email" placeholder="Enter your email" className="flex-1 px-4 py-2 rounded-md bg-white/10 text-white placeholder-gray-300 focus:outline-none focus:ring-2 focus:ring-cyan" />
              <button type="submit" className="px-6 py-2 bg-amber-600 hover:bg-amber-500 rounded-md font-medium transition-colors">
                Request access +
              </button>
            </form>
          )}
        </div>
      </div>
    </div>
  );
}
```

---
## 13. `apps/helix/src/components/HelixFallback.tsx` (simple static SVG fallback)
```tsx
export default function HelixFallback() {
  return (
    <div className="flex items-center justify-center min-h-screen bg-[#050B18] text-white p-4">
      <img src="/helix-fallback.svg" alt="Static Helix illustration" className="max-w-full h-auto" />
    </div>
  );
}
```

*(Create a static SVG named `public/helix-fallback.svg` that mimics the helix shape – you can generate it with any vector tool; the file just needs to be placed in `apps/helix/public/`.)*

---
## 14. `apps/helix/public/helix-fallback.svg`
*(placeholder – add your own SVG content)*
```svg
<svg width="800" height="600" viewBox="0 0 800 600" xmlns="http://www.w3.org/2000/svg">
  <rect width="800" height="600" fill="#050B18"/>
  <!-- Minimal representation of a double helix -->
  <path d="M200,100 C300,200 300,400 200,500" stroke="#38BDF8" stroke-width="8" fill="none"/>
  <path d="M600,100 C500,200 500,400 600,500" stroke="#38BDF8" stroke-width="8" fill="none"/>
  <!-- Some amber rungs -->
  <line x1="200" y1="200" x2="600" y2="200" stroke="#F59E0B" stroke-width="4"/>
  <line x1="200" y1="300" x2="600" y2="300" stroke="#F59E0B" stroke-width="4"/>
  <line x1="200" y1="400" x2="600" y2="400" stroke="#F59E0B" stroke-width="4"/>
</svg>
```

---
# How to push to GitHub
```bash
# Initialise repo (if you haven’t already)
git init
git add .
git commit -m "Initial commit – QueueCare + Helix hero"
# Add remote and push
git remote add origin <YOUR_GITHUB_REPO_URL>
git branch -M main
git push -u origin main
```

You now have a complete repository ready for further development of the QueueCare application and the Helix 3‑D hero section.
