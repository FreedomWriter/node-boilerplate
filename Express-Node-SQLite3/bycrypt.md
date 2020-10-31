[Bcrypt](https://www.npmjs.com/package/bcryptjs) is a key derivation library (Bcrypt is not an encryption library, it is a hashing library).

## Salting

Adding a random string (though it is not truly random because it needs to be able to be reproduced)

# To hash password using bcryptjs in the userModel on post

    npm i bcryptjs
    const bcrypt = require("bcryptjs")

    async function add(user) {
        user.password = await bcrypt.hash(user.password, 16)
        const [id] = await db("users").insert(user);
        return findById(id);
    }

//rounds is 2^16 === 65,536 times, a bit high. optimal is where it takes about 1 second

# To validate a hased password on login

    router.post("/login", async (req, res, next) => {
        try {
            const { username, password } = req.body;

            const user = await usersModel.findBy({ username }).first();

            const passwordValid = await bycrypt.compare(password, user.password);

            if (user && passwordValid) {
            res.status(200).json({ message: `Welcome ${user.username}!` });
            } else {
            res.status(401).json({ message: "Invalid Credentials" });
            }

        } catch (err) {
            next(err);
        }
    });

# Middleware Function that validates a user

    function restricted() {
        const authError = {
            message: "Invalid Credentials"
        };

        return async (req, res, next) => {
            try {
            const { username, password } = req.headers;
            if (!username || !password) {
                return res.status(401).json(authError);
            }
            const user = await usersModel.findBy({ username }).first();
            if (!user) {
                return res.status(401).json(authError);
            }
            const passwordValid = await bycrypt.compare(password, user.password);
            if (!passwordValid) {
                return res.status(401).json(authError);
            }
            // if we reach this point in the code we know that the user is authenticated
            next();
            } catch (err) {
            next(err);
            }
        };
    }
