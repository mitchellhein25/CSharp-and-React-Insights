---
title: "Creating an AuthProvider for Authentication in React"
date: 2023-06-08
---

# Creating an AuthProvider for Authentication in React

This article is a recap of the AuthProvider portion of the informative article [JWT Authentication in React with react-router](https://dev.to/sanjayttg/jwt-authentication-in-react-with-react-router-1d03?utm_source=reactdigest&utm_medium&utm_campaign=1655). It explores the integration of JWT authentication with React and react-router. This article will explore the first portion of the article, in which the following code is created:

{% highlight javascript %}
import axios from "axios";
import { createContext, useContext, useEffect, useMemo, useState } from "react";

const AuthContext = createContext();

const AuthProvider = ({ children }) => {
  // State to hold the authentication token
  const [token, setToken_] = useState(localStorage.getItem("token"));

  // Function to set the authentication token
  const setToken = (newToken) => {
    setToken_(newToken);
  };

  useEffect(() => {
    if (token) {
      axios.defaults.headers.common["Authorization"] = "Bearer " + token;
      localStorage.setItem('token',token);
    } else {
      delete axios.defaults.headers.common["Authorization"];
      localStorage.removeItem('token')
    }
  }, [token]);

  // Memoized value of the authentication context
  const contextValue = useMemo(
    () => ({
      token,
      setToken,
    }),
    [token]
  );

  // Provide the authentication context to the children components
  return (
    <AuthContext.Provider value={contextValue}>{children}</AuthContext.Provider>
  );
};

export const useAuth = () => {
  return useContext(AuthContext);
};

export default AuthProvider;
{% endhighlight %}

## createContext

The createContext hook allows you to create a global state to be shared between multiple components without having to pass props manually at each level of the component hierarchy. It provides a way to create a "context" object that can be accessed by any component within its tree. The first step in the code is creating an AuthContext.

## AuthProvider

The AuthProvider component serves as a wrapper around the children components and supplies them with the AuthContext through the AuthContext.Provider. 

### localStorage

localStorage allows us to store key-value pairs in the browser to persist the authentication token across browser refreshes.

### useState and setToken

The token is stored in local state with a standard setter to update it.
 
### useMemo

The useMemo hook allows for a value to be cached and only updated when the dependency changes. In this usage, the token will only be updated when it changes. The contextValue is then passed to the child element so it can access the token and setToken variables.

### useEffect

The useEffect hook in this case is setting the auth header in the browser if the token exists, and if the token is null it removes the header. It has the token in its dependency array, so it will be ran any time the token changes. The axios.defaults.headers.common portion is setting the header to be used in all axios calls.

### useAuth

The useAuth hook is a custom hook that simplifies the process of accessing the authentication context within any component wrapped by the AuthProvider. It allows the token and setToken function to be easily accessed.


These were the main points I wanted to cover from the article [JWT Authentication in React with react-router](https://dev.to/sanjayttg/jwt-authentication-in-react-with-react-router-1d03?utm_source=reactdigest&utm_medium&utm_campaign=1655).


