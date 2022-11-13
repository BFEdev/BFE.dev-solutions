
There is no solution yet.

Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/css/center-an-element-vertically_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)


.outer {
  /* your code here */
  display: flex;
  align-items: center;
  justify-content: center;
}

.inner {
  /* your code here */
}

<!-- OR -->

.outer {
  /* your code here */
  position: relative;
}

.inner {
  /* your code here */
  position: absolute;
  top: 50%;
  left: 50%;
  margin: -35px 0 0 -35px;
}

<!-- OR -->


.outer {
  /* your code here */
  position: relative;
}

.inner {

  /* your code here */
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
