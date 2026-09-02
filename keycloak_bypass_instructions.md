# Keycloak Bypass Instructions

Here are the 5 specific files where the Keycloak authentication logic was commented out to allow you to run the frontend independently.

To re-enable Keycloak, you just need to uncomment the original logic and remove the hardcoded overrides in each of these files:

### 1. `Frontend/src/auth/ProtectedRoute.tsx`
This is the global route guard.
**To re-enable:** Delete `return <Outlet />;` and uncomment the block above it.
```tsx
  const isLoggedIn = useAppSelector((state) => state.auth?.isLoggedIn);
  const location = useLocation();
  return isLoggedIn ? (
    <Outlet />
  ) : (
    <Navigate to={`/login?_r=${location.pathname}`} replace={true} />
  );
  // Remove: return <Outlet />;
```

### 2. `Frontend/src/application/token_calculator/auth/CanViewTokenCalculator.tsx`
This guards the view access for the Token Calculator.
**To re-enable:** Delete `return <Outlet />;` and uncomment the block above it.
```tsx
    const {
        isLoading,
        isError,
        data
    } = useUserinfoQuery()
    const token = useAppSelector((state) => state.keyClockAuthSlice.accessToken)
    const location = useLocation();
    const canView = data?.["ARE_TOKEN_CALCULATOR"]?.includes("view")
    if (!token) return <Navigate to={`/login?_r=${location.pathname}`} replace={true} />
    if (isLoading) return <LoadingAuthentication />
    if (isError) return <ErrorAuthentication />
    return canView ? (
        <Outlet />
    ) : (
        <Navigate to={`/${unauthorizedRoute}`} replace={true} />
    );
    // Remove: return <Outlet />;
```

### 3. `Frontend/src/application/token_calculator/auth/CanPerformTokenCalculator.tsx`
This guards access to the Developer/Admin routes (e.g. `/developer`).
**To re-enable:** Delete `return <Outlet />;` and uncomment the block above it.
```tsx
    const userDetails = useAppSelector((state) => state.keyClockAuthSlice.user);
    const location = useLocation();
    const canExecute = userDetails?.["ARE_TOKEN_CALCULATOR"]?.includes("edit") || userDetails?.["ARE_TOKEN_CALCULATOR"]?.includes("execute")
    if (!userDetails) return <Navigate to={`/login?_r=${location.pathname}`} replace={true} />
    return canExecute ? (
        <Outlet />
    ) : (
        <Navigate to={`/${unauthorizedRoute}`} replace={true} />
    );
    // Remove: return <Outlet />;
```

### 4. `Frontend/src/auth/slice/authSlice.tsx`
This holds the Redux initial state. We hardcoded it so the app thinks you are logged in as an admin.
**To re-enable:** Revert the `initialState` back to reading from local storage.
```tsx
const initialState = {
  isLoggedIn: loggedInUserDetails() ? true : false,
  role: loggedInUserDetails()?.role,
  isAdmin: loggedInUserDetails()?.isAdmin,
  rawUserDetails: loggedInUserDetails()?.rawUserDetails,
  roleDetails: loggedInUserDetails()?.roleDetails
};
```

### 5. `Frontend/src/application/token_calculator/TokenCalculatorLayout.tsx`
This hides/shows the sidebar options based on your Keycloak roles. We hardcoded `canExecute = true` to force the Developer and Model Selection tabs to show up.
**To re-enable:** Remove the hardcoded line and uncomment the logic.
```tsx
    const canExecute =
        userDetails?.["ARE_TOKEN_CALCULATOR"]?.includes("edit") ||
        userDetails?.["ARE_TOKEN_CALCULATOR"]?.includes("execute");
    // Remove: const canExecute = true; 
```
