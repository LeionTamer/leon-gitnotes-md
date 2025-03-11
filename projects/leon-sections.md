## Section Ideas

### Simple Hero Section

```typescript
function Hero1() {
  return (
    <div className="relative h-screen w-full overflow-hidden bg-gradient-to-br from-amber-50 to-orange-100">
      <div className="absolute inset-0 overflow-hidden">
        {Array.from({ length: 20 }).map((_, i) => (
          <motion.div
            key={i}
            className="absolute bg-amber-300/20 backdrop-blur-sm"
            style={{
              clipPath:
                "polygon(25% 0%, 75% 0%, 100% 50%, 75% 100%, 25% 100%, 0% 50%)",
              width: `${Math.random() * 100 + 50}px`,
              height: `${Math.random() * 100 + 50}px`,
              left: `${Math.random() * 100}%`,
              top: `${Math.random() * 100}%`,
            }}
            initial={{ opacity: 0, scale: 0 }}
            animate={{
              opacity: 0.3 + Math.random() * 0.3,
              scale: 0.8 + Math.random() * 0.5,
              x: Math.random() * 20 - 10,
              y: Math.random() * 20 - 10,
            }}
            transition={{
              duration: 2 + Math.random() * 3,
              repeat: Number.POSITIVE_INFINITY,
              repeatType: "reverse",
            }}
          />
        ))}
      </div>

      <div className="container relative z-10 mx-auto px-4 h-full flex flex-col justify-center items-center text-center">
        <motion.div
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.8 }}
          className="mb-6"
        >
          <Utensils className="h-16 w-16 mx-auto text-amber-500 mb-4" />
        </motion.div>

        <motion.h1
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.8, delay: 0.2 }}
          className="text-5xl md:text-7xl font-bold text-orange-600 mb-4"
        >
          Local Bites
        </motion.h1>

        <motion.p
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.8, delay: 0.4 }}
          className="text-xl md:text-2xl text-amber-800 max-w-2xl mb-8"
        >
          A guide to local eateries and restaurants from a local foodie
        </motion.p>

        <motion.div
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.8, delay: 0.6 }}
          className="flex flex-col sm:flex-row gap-4"
        >
          <Button className="bg-orange-500 hover:bg-orange-600 text-white">
            Discover Restaurants
          </Button>
          <Button
            variant="outline"
            className="border-amber-500 text-amber-700 hover:bg-amber-100"
          >
            View Food Map
          </Button>
        </motion.div>
      </div>
    </div>
  );
}
```

### Hero Section with Hexagonal Tile Background

```typescript
function Hero10() {
  // Create a honeycomb grid of hexagons
  const rows = 10;
  const cols = 15;
  const hexSize = 50;
  const hexagons = [];

  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      const xOffset = col * hexSize * 1.5;
      const yOffset =
        row * hexSize * Math.sqrt(3) +
        (col % 2) * ((hexSize * Math.sqrt(3)) / 2);

      hexagons.push({ x: xOffset, y: yOffset, row, col });
    }
  }

  return (
    <div className="relative h-screen w-full overflow-hidden bg-gradient-to-br from-amber-950 to-orange-900">
      <div className="absolute inset-0">
        <div className="absolute inset-0 overflow-hidden">
          {hexagons.map((hex, i) => (
            <motion.div
              key={i}
              className="absolute"
              style={{
                clipPath:
                  "polygon(25% 0%, 75% 0%, 100% 50%, 75% 100%, 25% 100%, 0% 50%)",
                width: `${hexSize}px`,
                height: `${(hexSize * Math.sqrt(3)) / 2}px`,
                left: `${hex.x}px`,
                top: `${hex.y}px`,
                backgroundColor: [
                  "rgba(251, 191, 36, 0.15)", // amber-400
                  "rgba(245, 158, 11, 0.15)", // amber-500
                  "rgba(249, 115, 22, 0.15)", // orange-500
                  "rgba(234, 88, 12, 0.15)", // orange-600
                  "rgba(252, 211, 77, 0.15)", // yellow-300
                ][Math.floor((hex.row + hex.col) % 5)],
              }}
              initial={{ opacity: 0, scale: 0.8 }}
              animate={{
                opacity: [0.2, 0.8, 0.2],
                scale: [0.8, 1, 0.8],
              }}
              transition={{
                duration: 4,
                repeat: Number.POSITIVE_INFINITY,
                repeatType: "loop",
                delay: (hex.row * 0.1 + hex.col * 0.1) % 2,
                ease: "easeInOut",
              }}
            />
          ))}
        </div>
      </div>

      <div className="container relative z-10 mx-auto px-4 h-full flex items-center justify-center">
        <div className="text-center max-w-3xl">
          <motion.div
            initial={{ opacity: 0, scale: 0.9 }}
            animate={{ opacity: 1, scale: 1 }}
            transition={{ duration: 0.5 }}
            className="relative inline-block mb-8"
          >
            <div
              className="relative w-24 h-24 mx-auto"
              style={{
                clipPath:
                  "polygon(25% 0%, 75% 0%, 100% 50%, 75% 100%, 25% 100%, 0% 50%)",
              }}
            >
              <motion.div
                className="absolute inset-0 bg-gradient-to-br from-amber-400 to-orange-500"
                animate={{
                  background: [
                    "linear-gradient(to bottom right, rgb(251, 191, 36), rgb(249, 115, 22))",
                    "linear-gradient(to bottom right, rgb(245, 158, 11), rgb(234, 88, 12))",
                    "linear-gradient(to bottom right, rgb(252, 211, 77), rgb(249, 115, 22))",
                    "linear-gradient(to bottom right, rgb(251, 191, 36), rgb(249, 115, 22))",
                  ],
                }}
                transition={{
                  duration: 8,
                  repeat: Number.POSITIVE_INFINITY,
                  repeatType: "loop",
                }}
              />
              <div className="absolute inset-0 flex items-center justify-center">
                <Utensils className="h-10 w-10 text-white" />
              </div>
            </div>
          </motion.div>

          <motion.h1
            initial={{ opacity: 0, y: 20 }}
            animate={{ opacity: 1, y: 0 }}
            transition={{ duration: 0.5, delay: 0.2 }}
            className="text-5xl md:text-7xl font-bold mb-4"
          >
            <motion.span
              className="text-amber-300"
              animate={{
                color: [
                  "rgb(252, 211, 77)", // yellow-300
                  "rgb(251, 191, 36)", // amber-400
                  "rgb(245, 158, 11)", // amber-500
                  "rgb(252, 211, 77)", // yellow-300
                ],
              }}
              transition={{
                duration: 8,
                repeat: Number.POSITIVE_INFINITY,
                repeatType: "loop",
              }}
            >
              Local
            </motion.span>{" "}
            <motion.span
              className="text-orange-400"
              animate={{
                color: [
                  "rgb(251, 146, 60)", // orange-400
                  "rgb(249, 115, 22)", // orange-500
                  "rgb(234, 88, 12)", // orange-600
                  "rgb(251, 146, 60)", // orange-400
                ],
              }}
              transition={{
                duration: 8,
                repeat: Number.POSITIVE_INFINITY,
                repeatType: "loop",
              }}
            >
              Bites
            </motion.span>
          </motion.h1>

          <motion.p
            initial={{ opacity: 0, y: 20 }}
            animate={{ opacity: 1, y: 0 }}
            transition={{ duration: 0.5, delay: 0.4 }}
            className="text-xl md:text-2xl text-amber-100 mb-12"
          >
            A guide to local eateries and restaurants from a local foodie
          </motion.p>

          <motion.div
            initial={{ opacity: 0, y: 20 }}
            animate={{ opacity: 1, y: 0 }}
            transition={{ duration: 0.5, delay: 0.6 }}
            className="relative max-w-xl mx-auto"
          >
            <Input
              placeholder="Enter your favorite cuisine..."
              className="pl-10 pr-32 py-6 rounded-full border-amber-700/50 bg-amber-900/50 text-amber-100 placeholder:text-amber-400/70 focus:border-amber-500 focus:ring-amber-500"
            />
            <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 h-5 w-5 text-amber-400" />
            <Button className="absolute right-1 top-1/2 transform -translate-y-1/2 rounded-full bg-gradient-to-r from-amber-500 to-orange-500 hover:from-amber-600 hover:to-orange-600 text-white">
              Discover
            </Button>
          </motion.div>

          <motion.div
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            transition={{ duration: 0.5, delay: 1 }}
            className="mt-12 flex flex-wrap justify-center gap-4"
          >
            {["Breakfast", "Lunch", "Dinner", "Dessert", "Drinks"].map(
              (category, i) => (
                <motion.div
                  key={category}
                  initial={{ opacity: 0, y: 10 }}
                  animate={{ opacity: 1, y: 0 }}
                  transition={{ delay: 1 + i * 0.1, duration: 0.3 }}
                  className="bg-amber-800/30 hover:bg-amber-700/40 px-4 py-2 rounded-full text-amber-200 cursor-pointer transition-colors"
                >
                  {category}
                </motion.div>
              )
            )}
          </motion.div>
        </div>
      </div>
    </div>
  );
}
```
