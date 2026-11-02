### Fixed
- `/topics` answered "This action could not be completed": the Host API client refused the topics door because it compared the path with its query. It now checks the path alone and still refuses one that resolves anywhere else (0.4.1).
