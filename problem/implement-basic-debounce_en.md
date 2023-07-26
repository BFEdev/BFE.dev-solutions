function debounce(func, wait) {
  let timer;

  return function(...args){
    if(timer){
      clearTimeout(timer);
    }
      timer=setTimeout(()=>{
      
        //func(..args) you can also go for this;
        
        func.apply(this,args);
      },wait);
  }
}
