Credits to chua_chee_cheng@aiap.sg and sunray team for providing the code


```python
Class ImageAlgorithmInterface(ABC):
    """
    The Strategy interface declares operations common to all supported versions
    of some algorithm.

    The Context uses this interface to call the algorithm defined by Concrete
    Strategies.
    """

    @abstractmethod
    def process(
        self,
        image: numpy.ndarray,
        params: Dict,
    ) -> numpy.ndarray:
        """
        Abstract method for Strategy interface. Concrete class need to override the
        algorithm implementation.
        """
        pass

    @abstractmethod
    def validate_params(self, params: Dict) -> bool:
        """
        Abstract method for Strategy interface. Concrete class need to override the
        validation of the parameters.
        """
        pass

    @abstractmethod
    def pass_requirement(self, params: Dict, path: str, metadata: pd.DataFrame) -> bool:
        """
        Abstract method for Strategy interface. Concrete class need to override the
        validation of the parameters.
        """
        pass
```

```python
class ImageProcessing:
    """
    The Context defines the interface of interest to clients.
    """

    def __init__(self, strategy: ImageAlgorithmInterface) -> None:
        """
        Usually, the Context accepts a strategy through the constructor, but
        also provides a setter to change it at runtime.
        """

        self._strategy = strategy

    @property
    def strategy(self) -> ImageAlgorithmInterface:
        """
        The Context maintains a reference to one of the Strategy objects. The
        Context does not know the concrete class of a strategy. It should work
        with all strategies via the Strategy interface.
        """

        return self._strategy

    @strategy.setter
    def strategy(self, strategy: ImageAlgorithmInterface) -> None:
        """
        Usually, the Context allows replacing a Strategy object at runtime.
        """

        self._strategy = strategy

    def validate_params(self, params: Dict) -> bool:
        """
        To validate the paramaters before running the alogrithm.

        Args:
            params (Dict[str, str]): The input parameters for the algorithm

        Returns:
            bool: Return True if all parameters are correct, return False, if there are
            invalid parameters
        """
        return self._strategy.validate_params(params)

    def pass_requirement(self, params: Dict, path: str, metadata: pd.DataFrame) -> bool:
        """_summary_

        Args:
            params (Dict): _description_
            path (str): _description_
            metadata (pd.DataFrame): _description_

        Returns:
            bool: _description_
        """
        return self._strategy.pass_requirement(params, path, metadata)

    def analyse(
        self,
        path: str,
        image: numpy.ndarray,
        params: Dict,
        metadata: pd.DataFrame,
    ) -> numpy.ndarray:
        """
        The Context delegates some work to the Strategy object instead of
        implementing multiple versions of the algorithm on its own.
        """

        processed_image = self._strategy.process(image, params)

        return processed_image
```

```python
Class Gaussian_Blur(ImageAlgorithmInterface):
    def validate_params(self, params: Dict) -> bool:
        logger.info(f"Validating Gaussian Blur params: {params}")

        class Parameter(BaseModel):
            sigma: float
            strength: float
            kernel_size: int

        try:
            Parameter(**params)
        except ValidationError as e:
            logger.error(e)
            return False

        logging.info("Parameters validation completed")
        return True

    def pass_requirement(self, params: Dict, path: str, metadata: DataFrame) -> bool:
        metadata["key"] = metadata["location"] + "/" + metadata["frame_number"]
        dataframe = metadata.loc[metadata["key"] == path]
        cutoff = dataframe["image_laplacian_variance"].values[0]

        if cutoff > params["cutoff"]:
            return False
        else:
            return True

    def process(self, image: np.ndarray, params: Dict) -> np.ndarray:
        """_summary_

        Args:
            data (List): _description_

        Returns:
            List: _description_
        """
        logger.info("Processing image using Gaussian Blur")
        sharpened_image = self._unsharp_mask(
            image,
            sigma=params["sigma"],
            strength=params["strength"],
            kernel_size=(9, 9),  ## TODO fix this kernel size
        )

        return sharpened_image

    def _unsharp_mask(
        self, image: np.ndarray, sigma: float, strength: float, kernel_size: tuple
    ):
        # Apply Gaussian blur with specified kernel size
        blurred = cv2.GaussianBlur(image, kernel_size, sigma)
        # Subtract the blurred image from the original
        sharpened = cv2.addWeighted(image, 1.0 + strength, blurred, -strength, 0)
        return sharpened
```



```python
def filter_blur(
    partitioned_input: Dict[str, Callable[[], Any]],
    method_params: Dict[str, str],
    algorithm_params: Dict[str, str],
    metadata: pd.DataFrame,
) -> Dict[str, Callable[[], Any]]:
    """_summary_

    Args:
        partitioned_input (Dict[str, Callable[[], Any]]): _description_
        params (Dict[str, str]): _description_
        metadata (pd.DataFrame): _description_

    Returns:
        Dict[str, Callable[[], Any]]: _description_
    """
    ## To determine the strategy via parameters ##
    blur = ImageProcessing(Gaussian_Blur())

    # Run validation check on the respective algorithm
    validate = blur.validate_params(algorithm_params)
    if validate:
        processed_dataset = {}
        for partition_key, partition_load_func in partitioned_input.items():
            # partition_key is the path and partition_data is image in numpy
            logger.info("Loading partition key: %s", partition_key)

            if blur.pass_requirement(method_params, partition_key, metadata):
                ## TODO Check which is the appropriate return result
                processed_dataset[partition_key] = partition_load_func
                continue

            partition_data = partition_load_func()  # load the actual partition data

            try:
                result = blur.analyse(
                    partition_key, partition_data, algorithm_params, metadata
                )
                processed_dataset[partition_key] = result

            except TypeError as err:
                ## TODO Consolidate all exception class / message
                logger.error("Parameters of input does not match function's parameters")
                raise err

        return processed_dataset
    else:
        logger.error("Failed validation check. Skipping this node.")
        return {}
```

```python
def filter_contrast(
    partitioned_input: Dict[str, Callable[[], Any]],
    method_params: Dict[str, str],
    algorithm_params: Dict[str, str],
    metadata: pd.DataFrame,
) -> Dict[str, Callable[[], Any]]:
    """_summary_

    Args:
        partitioned_input (Dict[str, Callable[[], Any]]): _description_
        images_to_remove (Union[Set[str], List[str]]): _description_

    Returns:
        _type_: _description_
    """

    ## To determine the strategy via parameters ##
    contrast = ImageProcessing(HDR_Debevec())

    # Run validation check on the respective algorithm
    validate = contrast.validate_params(algorithm_params)
    if validate:
        processed_dataset = {}
        for partition_key, partition_load_func in partitioned_input.items():
            # partition_key is the path and partition_data is image in numpy
            logger.info("Loading partition key: %s", partition_key)

            if contrast.pass_requirement(method_params, partition_key, metadata):
                processed_dataset[partition_key] = partition_load_func
                continue

            partition_data = partition_load_func()  # load the actual partition data

            try:
                result = contrast.analyse(
                    partition_key, partition_data, algorithm_params, metadata
                )
                ## TODO to implement persistence method of saving images
                ##contrast.save(partition_key, result)
                processed_dataset[partition_key] = result

            except TypeError as err:
                ## TODO Consolidate all exception class / message
                logger.error("Parameters of input does not match function's parameters")
                raise err

        return processed_dataset
    else:
        logger.error("Failed validation check. Skipping this node.")
        ## TODO Check which is the appropriate return result
        return {}
```