import json
import operator

class Test:
    def __init__(self):
        with open('C:\workspace\python\workspace.json') as f:
            self.folder_strcture_json = json.load(f)

        self.folder_list = {}
        self.setup_folder_structure_list('structure')
        self.setup_folder_structure_list('detail')
        self.setup_folder_structure_list('sort')


    def setup_folder_structure_list(self, type):
        for folder_id, folder_detail in self.folder_strcture_json.items():
            path_ids = list(folder_detail["pathIds"])
            if type == 'detail':
                self.fill_folder_structure(self.folder_list, path_ids, folder_detail)
            elif type == 'structure':
                self.init_folder_structure(self.folder_list, path_ids)
            elif type == 'sort':
                self.sort_folder_structure(self.folder_list, path_ids)
                temp_list = []
                for folder_detail in self.folder_list.values():
                    temp_list.append(folder_detail)
                temp_list = sorted(temp_list, key=lambda k: k.get('order', 0), reverse=False)
                self.folder_list = {}
                for folder in temp_list:
                    self.folder_list[folder["id"]] = folder
        print(self.folder_list)


    def fill_folder_structure(self, folder_list, path_ids, folder_detail):
        path_id = path_ids.pop(0)
        if len(path_ids) > 0:
            self.fill_folder_structure(folder_list[path_id]["subFolders"], path_ids, folder_detail)
        else:
            folder_detail["subFolders"] = folder_list[folder_detail["id"]]["subFolders"]
            folder_list[folder_detail["id"]] = folder_detail

    def init_folder_structure(self, folder_list, path_ids):
        path_id = path_ids.pop(0)
        if path_id not in folder_list:
            folder_list[path_id] = {}
            folder_list[path_id]["subFolders"] = {}

        if len(path_ids) > 0:
            self.init_folder_structure(folder_list[path_id]["subFolders"], path_ids)

    def sort_folder_structure(self, folder_list, path_ids):
        path_id = path_ids.pop(0)
        if len(path_ids) > 0:
            self.sort_folder_structure(folder_list[path_id]["subFolders"], path_ids)
        else:
            if len(folder_list[path_id]["subFolders"].values()) > 0:
                temp_list = []
                for folder_detail in folder_list[path_id]["subFolders"].values():
                    temp_list.append(folder_detail)
                temp_list = sorted(temp_list, key=lambda k: k.get('order', 0), reverse=False)
                folder_list[path_id]["subFolders"] = {}
                for folder in temp_list:
                    folder_list[path_id]["subFolders"][folder["id"]] = folder


    def extract_order(json):
        try:
            return json["order"]
        except KeyError:
            return 0


if __name__ == "__main__":
    test = Test()
