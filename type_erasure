#include <iostream>
#include <memory>
#include <vector>
#include <string>

// =============================================================
// Type Erasure
//
// Goal: store different types (Circle, Square, Text) in ONE vector,
// without making them inherit from a common base class.
//
// Idea: wrap each object in a small hidden class that knows its
// real type, and access all of them through one common interface.
//
// A concept makes the compiler reject types that have no draw().
// =============================================================

// A concept is a compile-time rule for a type.
// This one says: "T must have a draw() method we can call."
template <typename T>
concept HasDraw = requires(const T& t) { t.draw(); };

class Drawable {
private:
    // The common interface, hidden inside Drawable.
    // Users never see it; it exists only so we can call any
    // stored object through one pointer type.
    struct Concept {
        virtual ~Concept() = default;
        virtual void draw_impl() const = 0;
    };

    // A wrapper that holds the real object (a Circle, a Text, ...).
    // The compiler creates one Model for each type we store:
    // Model<Circle>, Model<Square>, Model<Text>.
    template <typename T>
    struct Model : Concept {
        T object; // the real object lives here

        Model(T obj) : object(std::move(obj)) {}

        // Forward the call to the real object's draw()
        void draw_impl() const override {
            object.draw();
        }
    };

    // Points to the Model that holds the real object.
    // Its type is Concept, so it can point to ANY Model<T>.
    std::unique_ptr<Concept> pimpl;

public:
    // Accepts any type T, wraps it in a Model<T>, and "forgets" T.
    // This forgetting is what "type erasure" means.
    //
    // "HasDraw T" instead of "typename T":
    // only types that pass the HasDraw rule are allowed.
    template <HasDraw T>
    Drawable(T obj)
        : pimpl(std::make_unique<Model<T>>(std::move(obj))) {}

    // The public function users call
    void draw() const {
        pimpl->draw_impl();
    }
};

// -------------------------------------------------------------
// Three unrelated types. No base class, no virtual functions.
// The only thing they share is a draw() method.
// -------------------------------------------------------------
struct Circle {
    void draw() const {
        std::cout << "Drawing a Circle ◯\n";
    }
};

struct Square {
    void draw() const {
        std::cout << "Drawing a Square ❏\n";
    }
};

struct Text {
    std::string content;
    void draw() const {
        std::cout << "Drawing Text: " << content << "\n";
    }
};

// -------------------------------------------------------------
// Usage
// -------------------------------------------------------------
int main() {
    // One vector, three different types inside
    std::vector<Drawable> shapes;

    shapes.push_back(Circle{});
    shapes.push_back(Square{});
    shapes.push_back(Text{"Hello Type Erasure!"});

    // Uncomment this line to see the concept at work:
    //It fails to compile because int has no draw().
    // shapes.push_back(42);

    for (const auto& shape : shapes) {
        shape.draw();
    }
}
